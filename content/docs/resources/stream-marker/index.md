---
title: "Stream Marker"
---

# Stream Marker

* TOC
{:toc}

Stream markers allows a User's position in a stream of Posts to be synced between multiple App.net clients. Then when you go from the browser to your phone, your stream is right where you left off. The current stream marker will be included in the [response envelope](/docs/basics/responses/#response-envelope) from any stream that returns Posts.

## Example Stream Marker

If a Stream Marker hasn't yet been set, you will receive the following format:

~~~ js
{
    "name": "global"
}
~~~

A marker that has been set will look like this:

~~~ js
{
    "id": "1234",
    "last_read_id": "2345",
    "name": "global",
    "percentage": 0,
    "updated_at": "2012-11-09T23:35:38Z",
    "version": "NWoZK3kTsExUV00Ywo1G5jlUKKs"
}
~~~

## Stream Marker fields

<table>
    <tr>
        <th>Field</th>
        <th>Type</th>
        <th>Description</th>
    </tr>
    <tr>
        <td><code>id</code></td>
        <td>string</td>
        <td>The Post id of the Post at the top of the stream when this Stream's position was last synced.</td>
    </tr>
    <tr>
        <td><code>last_read_id</code></td>
        <td>string</td>
        <td>The largest `id` the App.net server has seen for this stream marker. This is useful when determining what posts a user has seen regardless of whether they've scrolled backward in time. This value always increases unless it's <a href="">explicitly set backwards</a>.</td>
    </tr>
    <tr>
        <td><code>name</code></td>
        <td>string</td>
        <td>The name of the Stream this marker is for.</td>
    </tr>
    <tr>
        <td><code>percentage</code></td>
        <td>integer</td>
        <td>An optional field that indicate what percentage this post has been scrolled in the stream. It defaults to 0. This allows for very precise stream syncing but since different clients render Posts with different heights it won't be consistent across different clients.</td>
    </tr>
    <tr>
        <td><code>updated_at</code></td>
        <td>string</td>
        <td>The time this marker was last updated in <a href='http://en.wikipedia.org/wiki/ISO_8601'>ISO 8601</a> format.</td>
    </tr>
    <tr>
        <td><code>version</code></td>
        <td>string</td>
        <td>A unique identifier updated every time this Stream Marker is updated.</td>
    </tr>
</table>

## Update a Stream Marker

Update the User's current place in a Stream. To update a Stream Marker, you can POST an object that matches the [Stream Marker schema](/docs/resources/stream-marker/) with an HTTP header of ```Content-Type: application/json```. Only the ```id```, ```name```, and ```percentage``` fields will be used. ```name``` will come from the marker you received in the last GET request you made for the stream. You can update multiple stream markers at once by POSTing a list of stream markers instead of a single one.

The purpose of a Stream Marker is _not_ to allow a user to scroll a stream on one device and see the scroll happen on another device in realtime. A stream marker should only be updated when a user has stopped scrolling (i.e. the stream's position hasn't changed in multiple seconds) or when the app is being closed. Please make sure your code understands our [rate limit headers](/docs/basics/rate-limits/#response-headers) so if the rate limits for this endpoint change in the future your app handles this gracefully.

The `last_read_id` is updated if the provided `id` is larger than the current value of `last_read_id`. If you would like to explicitly set the `last_read_id` to a smaller value, you can pass the query string parameter `reset_read_id=1` when updating a stream marker.

<%= migration_warning ['response_envelope'] %>

<%= endpoint "POST", "posts/marker", "User" %>

#### Example

> POST https://alpha-api.app.net/stream/0/posts/marker
>
> Content-Type: application/json
> 
> DATA {"name": "global", "id": 2}

~~~ js
{
    "data": {
        "id": "2",
        "last_read_id": "2",
        "name": "global",
        "percentage": 0,
        "updated_at": "2012-11-12T20:04:58Z",
        "version": "d95o2uzYI7q7tY7bHI4U1xBug7s"
    },
    "meta": {
        "code": 200
    }
}
~~~