---
layout: post
title:  "Mocking boto in unittests"
date:   2018-11-01 12:32:13 +0100
author: Dan Wilson
---

Since starting working at co-op, everything we do is on AWS. If we need file storage, it’s going to be an S3 bucket. You need a queue, SQS. We also use TDD and run tests on every save and you don’t want [Boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html) trying to touch a file to S3 every time you save.

Enter [`unittest.mock`](https://docs.python.org/3/library/unittest.mock.html)

Having never used a mock before, I had no idea what I was doing. I couldn’t understand the concept of why I would use a fake object over the real thing. But mocks go further than that. You can mock functions and methods, and this is fantastic for when you want to test your Boto stuff.

## Mock.Patch
We use the `@mock.patch()` decorators on our test methods to patch `botocore.client.BaseClient._make_api_call` with a mock object.
Let’s have a look at the test below:

```python
@mock.patch('botocore.client.BaseClient._make_api_call')
def test_submit_object_to_queue(self, mock_api_call):

    object = get_object_from_json(self.data)
    pickled_object = pickle_object(object)
    submit_object_to_queue(object)

    mock_api_call.assert_called_with('SendMessage', {
        'QueueUrl': ENDPOINT_URL,
        'MessageBody': pickled_object
    })
```

Here we’re patching the part of Boto that makes the API call to AWS, and we can make sure that it’s calling the correct service, with the correct query. You can find out more information about the AWS API in the API reference on each AWS service in the docs [here](https://docs.aws.amazon.com/index.html#lang/en_us).

## Testing failures
With mocks we can define return values as well as side effects. One of the main reasons we use side effects is to test how our code handles errors. In the side effect we can raise an error, and see what happens. As for Boto, botocore has a `ClientError` exceptions.

## Gotchas
### Multiple calls
```python
def submit_object_to_queue(object: object) -> HTTPStatus:
    """ submit object to backend """

    pickled_object = pickle_object(object)

    sqs = boto3.resource('sqs', endpoint_url=SQS_ENDPOINT_URL)
    queue = sqs.get_queue_by_name(QueueName=QUEUE_NAME)

    try:
        queue.send_message(MessageBody=str(pickled_object))
        status = HTTPStatus.ACCEPTED
    except ClientError as err:
        status = HTTPStatus.INTERNAL_SERVER_ERROR

    return status
```

Mocking the `botocore.client.BaseClient._make_api_call` on a test case for this method could prove difficult because it’s actually making two calls to the API.

* `sqs.get_queue_by_name(QueueName=QUEUE_NAME)`
* `queue.send_message(MessageBody=pickled_object)`

If were to write the following test:
```python
@mock.patch('botocore.client.BaseClient._make_api_call')
def test_submit_object_to_queue(self, mock_api_call):

    object = get_object_from_json(self.data)
    pickled_object = pickle_object(object)
    submit_object_to_queue(object)

		mock_api_call.assert_called_with('GetQueueUrl', {
        'QueueName': QUEUE_NAME
    })

    mock_api_call.assert_called_with('SendMessage', {
        'QueueUrl': ENDPOINT_URL,
        'MessageBody': pickled_object
    })
```

It would fail because when you use `assert_called_with` it only checks the last time your mock was called. To get around this we can use `assert_any_call` which will check any of the times your mock was called.

I find this not to be a problem though as it can help you structure your code. Usually if it’s hard to write a test, your code is over complicated. Consider the following refactor of `submit_object_to_queue()`:

```python
@app.route('/create', methods=['POST'])
def create():
    object = get_object(request.json)
    queue = get_queue(QUEUE_NAME)

    status submit_object_to_queue(object, queue)

    return status

def submit_object_to_queue(object: object, queue) -> HTTPStatus:
    """ submit object to backend """

    pickled_object = pickle_object(object)

    try:
        queue.send_message(MessageBody=str(pickled_object))
        status = HTTPStatus.ACCEPTED
    except ClientError as err:
        status = HTTPStatus.INTERNAL_SERVER_ERROR

    return status

def get_queue(queue_name):
    """ get SQS queue """
    sqs = boto3.resource('sqs', endpoint_url=SQS_ENDPOINT_URL)
    queue = sqs.get_queue_by_name(QueueName=QUEUE_NAME)
    return queue
```

Now that the AWS API calls are in different functions, and therefore will be in different `unittests`, not only will our testing be easier to mock, but our code is actually better represented to what is actually going on.

### Where to patch
Sometimes it’s quite (read: very) hard to find what you’re actually trying to mock. You can mock almost anything from almost anywhere. But finding _what and where_ is the hard part. I still haven’t mastered this yet, and I know a lot of better developers than me that also haven’t. So there’s not much I can tell you on this front, other than “Don’t give up”. You’ll often find yourself battling with which object to mock, what side effect to give it but you’ll get there, and then your test should *always work*.

## To mock or not to mock?
This was my initial concern with mocking. I felt like my application wasn’t being fully tested because I was almost bending the rules. However, unittests are meant to test small “units” of code, so mocking an object removes excess code from my tests and focuses hard on the specific unit I’m testing. This can cause inconsistencies.

One mistake I’ve made, even whilst pairing, was to use a different shape or type of object. My function was going to receive a `namedtuple` however in my test I was mocking a `dict` like object. This lead me to write a couple of functions that were working with a `dict` like object, but when I ran the application it obviously didn’t work. Of course, if we implemented type checking, or I wasn’t incompetent, this wouldn’t have happened.

When it comes down to integration, functional, or end to end tests I would advise **against** mocking. While your test will run faster, you’re trading confidence in your test for a marginal increase in build time.


---
## Continue the conversation
Let me know what you think about mocking by tweeting me [@danwlsn 👋](http://twitter.com/danwlsn)
