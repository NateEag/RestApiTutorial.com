+++
title = "HTTP Status Codes"
weight = 40
+++
This page is created from HTTP status code information found at [ietf.org](https://www.ietf.org/assignments/http-status-codes/http-status-codes.xml) and [Wikipedia](https://en.wikipedia.org/wiki/HTTP_status_code). Click on the category heading or the status code link to read more.

## 1xx Informational
This class of status code indicates a provisional response, consisting only of the Status-Line and optional headers, and is terminated by an empty line. There are no required headers for this class of status code. Since `HTTP/1.0` did not define any `1xx` status codes, servers MUST NOT send a `1xx` response to an `HTTP/1.0` client except under experimental conditions.

A client MUST be prepared to accept one or more `1xx` status responses prior to a regular response, even if the client does not expect a `100 (Continue)` status message. Unexpected `1xx` status responses MAY be ignored by a user agent.

Proxies MUST forward `1xx` responses, unless the connection between the proxy and its client has been closed, or unless the proxy itself requested the generation of the `1xx` response. (For example, if a proxy adds a `Expect: 100-continue` field when it forwards a request, then it need not forward the corresponding `100 (Continue)` response(s).)

{{% expand title="100 Continue" %}}
The client SHOULD continue with its request. This interim response is used to inform the client that the initial part of the request has been received and has not yet been rejected by the server. The client SHOULD continue by sending the remainder of the request or, if the request has already been completed, ignore this response. The server MUST send a final response after the request has been completed. See [section 8.2.3] for detailed discussion of the use and handling of this status code.

This means that the server has received the request headers, and that the client should proceed to send the request body (in the case of a request for which a body needs to be sent; for example, a `POST` request). If the request body is large, sending it to a server when a request has already been rejected based upon inappropriate headers is inefficient. To have a server check if the request could be accepted based on the request's headers alone, a client must send `Expect: 100-continue` as a header in its initial request and check if a `100 Continue` status code is received in response before continuing (or receive `417 Expectation Failed` and not continue).
{{% /expand %}}

{{% expand title="101 Switching Protocols" %}}
This means the requester has asked the server to switch protocols and the server is acknowledging that it will do so.

The server understands and is willing to comply with the client's request, via the Upgrade message header field (section 14.42), for a change in the application protocol being used on this connection. The server will switch protocols to those defined by the response's Upgrade header field immediately after the empty line which terminates the `101` response.

The protocol SHOULD be switched only when it is advantageous to do so. For example, switching to a newer version of `HTTP` is advantageous over older versions, and switching to a real-time, synchronous protocol might be advantageous when delivering resources that use such features.
{{% /expand %}}

{{% expand title="102 Processing (WebDAV)" %}}
The `102 (Processing)` status code is an interim response used to inform the client that the server has accepted the complete request, but has not yet completed it. This status code SHOULD only be sent when the server has a reasonable expectation that the request will take significant time to complete. As guidance, if a method is taking longer than 20 seconds (a reasonable, but arbitrary value) to process the server SHOULD return a `102 (Processing)` response. The server MUST send a final response after the request has been completed.

Methods can potentially take a long period of time to process, especially methods that support the Depth header. In such cases the client may time-out the connection while waiting for a response. To prevent this the server may return a `102 (Processing)` status code to indicate to the client that the server is still processing the method.

Wikipedia
As a `WebDAV` request may contain many sub-requests involving file operations, it may take a long time to complete the request. This code indicates that the server has received and is processing the request, but no response is available yet. This prevents the client from timing out and assuming the request was lost.
{{% /expand %}}

{{% expand "226 IM Used" %}}
The server has fulfilled a GET request for the resource, and the response is a representation of the result of one or more instance-manipulations applied to the current instance.  The actual current instance might not be available except by combining this response with other previous or future responses, as appropriate for the specific instance-manipulation(s).  If so, the headers of the resulting instance are the result of combining the headers from the status-226 response and the other instances, following the rules in section 13.5.3 of the HTTP/1.1 specification.

The request MUST have included an A-IM header field listing at least one instance-manipulation.  The response MUST include an Etag header field giving the entity tag of the current instance.

A response received with a status code of 226 MAY be stored by a cache and used in reply to a subsequent request, subject to the HTTP expiration mechanism and any Cache-Control headers, and to the requirements in section 10.6.

A response received with a status code of 226 MAY be used by a cache, in conjunction with a cache entry for the base instance, to create a cache entry for the current instance.
{{% /expand %}}

## 3xx Redirection
This class of status code indicates that further action needs to be taken by the client in order to fulfill the request. The action required MAY be carried out by the user agent without interaction with the user if and only if the method used in the second request is `GET` or `HEAD`. A client SHOULD detect infinite redirection loops, since such loops generate network traffic for each redirection.

> __Note:__ previous versions of this specification recommended a maximum of five redirections. Content developers should be aware that there might be clients that implement such a fixed limitation.

300 Multiple Choices</a>
<div id="multiplechoices" class="collapse">
<p>The requested resource corresponds to any one of a set of representations, each with its own specific location, and agent- driven negotiation information (section 12) is being provided so that the user (or user agent) can select a preferred representation and redirect its request to that location.</p>
<p>Unless it was a HEAD request, the response SHOULD include an entity containing a list of resource characteristics and location(s) from which the user or user agent can choose the one most appropriate. The entity format is specified by the media type given in the Content- Type header field. Depending upon the format and the capabilities of the user agent, selection of the most appropriate choice MAY be performed automatically. However, this specification does not define any standard for such automatic selection.</p>
<p>If the server has a preferred choice of representation, it SHOULD include the specific URI for that representation in the Location field; user agents MAY use the Location field value for automatic redirection. This response is cacheable unless indicated otherwise.</p>
<h3>Wikipedia</h3>
<p>Indicates multiple options for the resource that the client may follow. It, for instance, could be used to present different format options for video, list files with different extensions, or word sense disambiguation.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#movepermanently">301 Moved Permanently</a>
<div id="movepermanently" class="collapse">
<p>The requested resource has been assigned a new permanent URI and any future references to this resource SHOULD use one of the returned URIs. Clients with link editing capabilities ought to automatically re-link references to the Request-URI to one or more of the new references returned by the server, where possible. This response is cacheable unless indicated otherwise.</p>
<p>The new permanent URI SHOULD be given by the Location field in the response. Unless the request method was HEAD, the entity of the response SHOULD contain a short hypertext note with a hyperlink to the new URI(s).</p>
<p>If the 301 status code is received in response to a request other than GET or HEAD, the user agent MUST NOT automatically redirect the request unless it can be confirmed by the user, since this might change the conditions under which the request was issued.</p>
<blockquote><strong>Note:</strong> When automatically redirecting a POST request after receiving a 301 status code, some existing HTTP/1.0 user agents will erroneously change it into a GET request.</blockquote>
<h3>Wikipedia</h3>
<p>This and all future requests should be directed to the given URI.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#found">302 Found</a>
<div id="found" class="collapse">
<p>The requested resource resides temporarily under a different URI. Since the redirection might be altered on occasion, the client SHOULD continue to use the Request-URI for future requests. This response is only cacheable if indicated by a Cache-Control or Expires header field.</p>
<p>The temporary URI SHOULD be given by the Location field in the response. Unless the request method was HEAD, the entity of the response SHOULD contain a short hypertext note with a hyperlink to the new URI(s).</p>
<p>If the 302 status code is received in response to a request other than GET or HEAD, the user agent MUST NOT automatically redirect the request unless it can be confirmed by the user, since this might change the conditions under which the request was issued.</p>
<blockquote><strong>Note:</strong> RFC 1945 and RFC 2068 specify that the client is not allowed to change the method on the redirected request.  However, most existing user agent implementations treat 302 as if it were a 303 response, performing a GET on the Location field-value regardless of the original request method. The status codes 303 and 307 have been added for servers that wish to make unambiguously clear which kind of reaction is expected of the client.</blockquote>
<h3>Wikipedia</h3>
<p>This is an example of industry practice contradicting the standard.[2] The HTTP/1.0 specification (RFC 1945) required the client to perform a temporary redirect (the original describing phrase was "Moved Temporarily"), but popular browsers implemented 302 with the functionality of a 303 See Other. Therefore, HTTP/1.1 added status codes 303 and 307 to distinguish between the two behaviours. However, some Web applications and frameworks use the 302 status code as if it were the 303.</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<a data-toggle="collapse" data-target="#see_other">303 See Other</a>
<div id="see_other" class="collapse">
<p>The response to the request can be found under a different URI and SHOULD be retrieved using a GET method on that resource. This method exists primarily to allow the output of a POST-activated script to redirect the user agent to a selected resource. The new URI is not a substitute reference for the originally requested resource. The 303 response MUST NOT be cached, but the response to the second (redirected) request might be cacheable.</p>
<p>The different URI SHOULD be given by the Location field in the response. Unless the request method was HEAD, the entity of the response SHOULD contain a short hypertext note with a hyperlink to the new URI(s).</p>
<blockquote>Note: Many pre-HTTP/1.1 user agents do not understand the 303 status. When interoperability with such clients is a concern, the 302 status code may be used instead, since most user agents react to a 302 response as described here for 303.</blockquote>
<h3>Wikipedia</h3>
<p>The response to the request can be found under another URI using a GET method. When received in response to a POST (or PUT/DELETE), it should be assumed that the server has received the data and the redirect should be issued with a separate GET message.</p>
<p>Since HTTP/1.1</p>
</div>
</div>
<div class="span4">
<i class="icon-star"></i> <a data-toggle="collapse" data-target="#not_modified">304 Not Modified</a>
<div id="not_modified" class="collapse">
<p>If the client has performed a conditional GET request and access is allowed, but the document has not been modified, the server SHOULD respond with this status code. The 304 response MUST NOT contain a message-body, and thus is always terminated by the first empty line after the header fields.</p>
<p>The response MUST include the following header fields:</p>
<ul>
<li>Date, unless its omission is required by section 14.18.1</li>
</ul>
<p>If a clockless origin server obeys these rules, and proxies and clients add their own Date to any response received without one (as already specified by [RFC 2068], section 14.19), caches will operate correctly.</p>
<ul>
<li>ETag and/or Content-Location, if the header would have been sent in a 200 response to the same request</li>
<li>Expires, Cache-Control, and/or Vary, if the field-value might differ from that sent in any previous response for the same variant</li>
</ul>
<p>If the conditional GET used a strong cache validator (see section 13.3.3), the response SHOULD NOT include other entity-headers. Otherwise (i.e., the conditional GET used a weak validator), the response MUST NOT include other entity-headers; this prevents inconsistencies between cached entity-bodies and updated headers.</p>
<p>If a 304 response indicates an entity not currently cached, then the cache MUST disregard the response and repeat the request without the conditional.</p>
<p>If a cache uses a received 304 response to update a cache entry, the cache MUST update the entry to reflect any new field values given in the response.</p>
<h3>Wikipedia</h3>
<p>Indicates the resource has not been modified since last requested. Typically, the HTTP client provides a header like the If-Modified-Since header to provide a time against which to compare. Using this saves bandwidth and reprocessing on both the server and client, as only the header data must be sent and received in comparison to the entirety of the page being re-processed by the server, then sent again using more bandwidth of the server and client.</p>
<p><i class="icon-star"></i> Used for conditional GET calls to reduce band-width usage. If used, must set the Date, Content-Location, ETag headers to what they would have been on a regular GET call.  There must be no body on the response.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#use_proxy">305 Use Proxy</a>
<div id="use_proxy" class="collapse">
<p>The requested resource MUST be accessed through the proxy given by the Location field. The Location field gives the URI of the proxy. The recipient is expected to repeat this single request via the proxy. 305 responses MUST only be generated by origin servers.</p>
<blockquote>Note: RFC 2068 was not clear that 305 was intended to redirect a single request, and to be generated by origin servers only.  Not observing these limitations has significant security consequences.</blockquote>
<h3>Wikipedia</h3>
<p>Many HTTP clients (such as Mozilla and Internet Explorer) do not correctly handle responses with this status code, primarily for security reasons.</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<a data-toggle="collapse" data-target="#306unused">306 (Unused)</a>
<div id="306unused" class="collapse">
<p>The 306 status code was used in a previous version of the specification, is no longer used, and the code is reserved.</p>
<h3>Wikipedia</h3>
<p>No longer used. Originally meant &quot;Subsequent requests should use the specified proxy.&quot;</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#temp_redirect">307 Temporary Redirect</a>
<div id="temp_redirect" class="collapse">
<p>The requested resource resides temporarily under a different URI. Since the redirection MAY be altered on occasion, the client SHOULD continue to use the Request-URI for future requests. This response is only cacheable if indicated by a Cache-Control or Expires header field.</p>
<p>The temporary URI SHOULD be given by the Location field in the response. Unless the request method was HEAD, the entity of the response SHOULD contain a short hypertext note with a hyperlink to the new URI(s) , since many pre-HTTP/1.1 user agents do not understand the 307 status. Therefore, the note SHOULD contain the information necessary for a user to repeat the original request on the new URI.</p>
<p>If the 307 status code is received in response to a request other than GET or HEAD, the user agent MUST NOT automatically redirect the request unless it can be confirmed by the user, since this might change the conditions under which the request was issued.</p>
<h3>Wikipedia</h3>
<p>In this case, the request should be repeated with another URI; however, future requests can still use the original URI. In contrast to 302, the request method should not be changed when reissuing the original request. For instance, a POST request must be repeated using another POST request.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#perm_redirect">308 Permanent Redirect (experimental)</a>
<div id="perm_redirect" class="collapse">
<h3>Wikipedia</h3>
<p>The request, and all future requests should be repeated using another URI. 307 and 308 (as proposed) parallel the behaviours of 302 and 301, but do not require the HTTP method to change. So, for example, submitting a form to a permanently redirected resource may continue smoothly.</p>


## 4xx Client Error
The `4xx` class of status code is intended for cases in which the client seems to have erred. Except when responding to a `HEAD` request, the server SHOULD include an entity containing an explanation of the error situation, and whether it is a temporary or permanent condition. These status codes are applicable to any request method. Clients SHOULD display any included entity to the user.

If the client is sending data, a server implementation using `TCP` SHOULD be careful to ensure that the client acknowledges receipt of the packet(s) containing the response, before the server closes the input connection. If the client continues sending data to the server after the close, the server's `TCP` stack will send a reset packet to the client, which may erase the client's unacknowledged input buffers before they can be read and interpreted by the `HTTP` application.

<i class="icon-star"></i> <a data-toggle="collapse" data-target="#bad_request">400 Bad Request</a>
<div id="bad_request" class="collapse">
<p>The request could not be understood by the server due to malformed syntax. The client SHOULD NOT repeat the request without modifications.</p>
<h3>Wikipedia</h3>
<p>The request cannot be fulfilled due to bad syntax.</p>
<p><i class="icon-star"></i> General error when fulfilling the request would cause an invalid state. Domain validation errors, missing data, etc. are some examples.</p>
</div>
</div>
<div class="span4">
<i class="icon-star"></i> <a data-toggle="collapse" data-target="#unauthorized">401 Unauthorized</a>
<div id="unauthorized" class="collapse">
<p>The request requires user authentication. The response MUST include a WWW-Authenticate header field (section 14.47) containing a challenge applicable to the requested resource. The client MAY repeat the request with a suitable Authorization header field (section 14.8). If the request already included Authorization credentials, then the 401 response indicates that authorization has been refused for those credentials. If the 401 response contains the same challenge as the prior response, and the user agent has already attempted authentication at least once, then the user SHOULD be presented the entity that was given in the response, since that entity might include relevant diagnostic information. HTTP access authentication is explained in "HTTP Authentication: Basic and Digest Access Authentication".</p>
<h3>Wikipedia</h3>
<p>Similar to 403 Forbidden, but specifically for use when authentication is possible but has failed or not yet been provided. The response must include a WWW-Authenticate header field containing a challenge applicable to the requested resource. See Basic access authentication and Digest access authentication.</p>
<p><i class="icon-star"></i> Error code response for missing or invalid authentication token.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#pmt_required">402 Payment Required</a>
<div id="pmt_required" class="collapse">
<p>This code is reserved for future use.</p>
<h3>Wikipedia</h3>
<p>Reserved for future use. The original intention was that this code might be used as part of some form of digital cash or micropayment scheme, but that has not happened, and this code is not usually used. As an example of its use, however, Apple's MobileMe service generates a 402 error ("httpStatusCode:402" in the Mac OS X Console log) if the MobileMe account is delinquent.</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<i class="icon-star"></i> <a data-toggle="collapse" data-target="#forbidden">403 Forbidden</a>
<div id="forbidden" class="collapse">
<p>The server understood the request, but is refusing to fulfill it. Authorization will not help and the request SHOULD NOT be repeated. If the request method was not HEAD and the server wishes to make public why the request has not been fulfilled, it SHOULD describe the reason for the refusal in the entity. If the server does not wish to make this information available to the client, the status code 404 (Not Found) can be used instead.</p>
<h3>Wikipedia</h3>
<p>The request was a legal request, but the server is refusing to respond to it. Unlike a 401 Unauthorized response, authenticating will make no difference.</p>
<p><i class="icon-star"></i> Error code for user not authorized to perform the operation or the resource is unavailable for some reason (e.g. time constraints, etc.).</p>
</div>
</div>
<div class="span4">
<i class="icon-star"></i> <a data-toggle="collapse" data-target="#not_found">404 Not Found</a>
<div id="not_found" class="collapse">
<p>The server has not found anything matching the Request-URI. No indication is given of whether the condition is temporary or permanent. The 410 (Gone) status code SHOULD be used if the server knows, through some internally configurable mechanism, that an old resource is permanently unavailable and has no forwarding address. This status code is commonly used when the server does not wish to reveal exactly why the request has been refused, or when no other response is applicable.</p>
<h3>Wikipedia</h3>
<p>The requested resource could not be found but may be available again in the future. Subsequent requests by the client are permissible.</p>
<p><i class="icon-star"></i> Used when the requested resource is not found, whether it doesn't exist or if there was a 401 or 403 that, for security reasons, the service wants to mask.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#method_no_allowed">405 Method Not Allowed</a>
<div id="method_no_allowed" class="collapse">
<p>The method specified in the Request-Line is not allowed for the resource identified by the Request-URI. The response MUST include an Allow header containing a list of valid methods for the requested resource.</p>
<h3>Wikipedia</h3>
<p>A request was made of a resource using a request method not supported by that resource; for example, using GET on a form which requires data to be presented via POST, or using PUT on a read-only resource.</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<a data-toggle="collapse" data-target="#not_acceptable">406 Not Acceptable</a>
<div id="not_acceptable" class="collapse">
<p>The resource identified by the request is only capable of generating response entities which have content characteristics not acceptable according to the accept headers sent in the request.</p>
<p>Unless it was a HEAD request, the response SHOULD include an entity containing a list of available entity characteristics and location(s) from which the user or user agent can choose the one most appropriate. The entity format is specified by the media type given in the Content-Type header field. Depending upon the format and the capabilities of the user agent, selection of the most appropriate choice MAY be performed automatically. However, this specification does not define any standard for such automatic selection.</p>
<blockquote>Note: HTTP/1.1 servers are allowed to return responses which are not acceptable according to the accept headers sent in the request. In some cases, this may even be preferable to sending a 406 response. User agents are encouraged to inspect the headers of an incoming response to determine if it is acceptable.</blockquote>
<p>If the response could be unacceptable, a user agent SHOULD temporarily stop receipt of more data and query the user for a decision on further actions.</p>
<h3>Wikipedia</h3>
<p>The requested resource is only capable of generating content not acceptable according to the Accept headers sent in the request.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#proxy_auth_rqd">407 Proxy Authentication Required</a>
<div id="proxy_auth_rqd" class="collapse">
<p>This code is similar to 401 (Unauthorized), but indicates that the client must first authenticate itself with the proxy. The proxy MUST return a Proxy-Authenticate header field (section 14.33) containing a challenge applicable to the proxy for the requested resource. The client MAY repeat the request with a suitable Proxy-Authorization header field (section 14.34). HTTP access authentication is explained in "HTTP Authentication: Basic and Digest Access Authentication".</p>
<h3>Wikipedia</h3>
<p>The client must first authenticate itself with the proxy.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#request_timeout">408 Request Timeout</a>
<div id="request_timeout" class="collapse">
<p>The client did not produce a request within the time that the server was prepared to wait. The client MAY repeat the request without modifications at any later time.</p>
<h3>Wikipedia</h3>
<p>The server timed out waiting for the request. According to W3 HTTP specifications: &quot;The client did not produce a request within the time that the server was prepared to wait. The client MAY repeat the request without modifications at any later time.&quot;</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<i class="icon-star"></i> <a data-toggle="collapse" data-target="#conflict">409 Conflict</a>
<div id="conflict" class="collapse">
<p>The request could not be completed due to a conflict with the current state of the resource. This code is only allowed in situations where it is expected that the user might be able to resolve the conflict and resubmit the request. The response body SHOULD include enough information for the user to recognize the source of the conflict. Ideally, the response entity would include enough information for the user or user agent to fix the problem; however, that might not be possible and is not required.</p>
<p>Conflicts are most likely to occur in response to a PUT request. For example, if versioning were being used and the entity being PUT included changes to a resource which conflict with those made by an earlier (third-party) request, the server might use the 409 response to indicate that it can't complete the request. In this case, the response entity would likely contain a list of the differences between the two versions in a format defined by the response Content-Type.</p>
<h3>Wikipedia</h3>
<p>Indicates that the request could not be processed because of conflict in the request, such as an edit conflict.</p>
<p><i class="icon-star"></i> Whenever a resource conflict would be caused by fulfilling the request. Duplicate entries and deleting root objects when cascade-delete is not supported are a couple of examples.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#gone">410 Gone</a>
<div id="gone" class="collapse">
<p>The requested resource is no longer available at the server and no forwarding address is known. This condition is expected to be considered permanent. Clients with link editing capabilities SHOULD delete references to the Request-URI after user approval. If the server does not know, or has no facility to determine, whether or not the condition is permanent, the status code 404 (Not Found) SHOULD be used instead. This response is cacheable unless indicated otherwise.</p>
<p>The 410 response is primarily intended to assist the task of web maintenance by notifying the recipient that the resource is intentionally unavailable and that the server owners desire that remote links to that resource be removed. Such an event is common for limited-time, promotional services and for resources belonging to individuals no longer working at the server's site. It is not necessary to mark all permanently unavailable resources as "gone" or to keep the mark for any length of time -- that is left to the discretion of the server owner.</p>
<h3>Wikipedia</h3>
<p>Indicates that the resource requested is no longer available and will not be available again. This should be used when a resource has been intentionally removed and the resource should be purged. Upon receiving a 410 status code, the client should not request the resource again in the future. Clients such as search engines should remove the resource from their indices. Most use cases do not require clients and search engines to purge the resource, and a &quot;404 Not Found&quot; may be used instead.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#length_rqd">411 Length Required</a>
<div id="length_rqd" class="collapse">
<p>The server refuses to accept the request without a defined Content- Length. The client MAY repeat the request if it adds a valid Content-Length header field containing the length of the message-body in the request message.</p>
<h3>Wikipedia</h3>
<p>The request did not specify the length of its content, which is required by the requested resource.</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<a data-toggle="collapse" data-target="#precondition_failed">412 Precondition Failed</a>
<div id="precondition_failed" class="collapse">
<p>The precondition given in one or more of the request-header fields evaluated to false when it was tested on the server. This response code allows the client to place preconditions on the current resource metainformation (header field data) and thus prevent the requested method from being applied to a resource other than the one intended.</p>
<h3>Wikipedia</h3>
<p>The server does not meet one of the preconditions that the requester put on the request.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#request_entity_too_large">413 Request Entity Too Large</a>
<div id="request_entity_too_large" class="collapse">
<p>The server is refusing to process a request because the request entity is larger than the server is willing or able to process. The server MAY close the connection to prevent the client from continuing the request.</p>
<p>If the condition is temporary, the server SHOULD include a Retry- After header field to indicate that it is temporary and after what time the client MAY try again.</p>
<h3>Wikipedia</h3>
<p>The request is larger than the server is willing or able to process.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#request_uri_too_long">414 Request-URI Too Long</a>
<div id="request_uri_too_long" class="collapse">
<p>The server is refusing to service the request because the Request-URI is longer than the server is willing to interpret. This rare condition is only likely to occur when a client has improperly converted a POST request to a GET request with long query information, when the client has descended into a URI "black hole" of redirection (e.g., a redirected URI prefix that points to a suffix of itself), or when the server is under attack by a client attempting to exploit security holes present in some servers using fixed-length buffers for reading or manipulating the Request-URI.</p>
<h3>Wikipedia</h3>
<p>The URI provided was too long for the server to process.</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<a data-toggle="collapse" data-target="#unsupported_media_type">415 Unsupported Media Type</a>
<div id="unsupported_media_type" class="collapse">
<p>The server is refusing to service the request because the entity of the request is in a format not supported by the requested resource for the requested method.</p>
<h3>Wikipedia</h3>
<p>The request entity has a media type which the server or resource does not support. For example, the client uploads an image as image/svg+xml, but the server requires that images use a different format.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#requested_range_not_satisfiable">416 Requested Range Not Satisfiable</a>
<div id="requested_range_not_satisfiable" class="collapse">
<p>A server SHOULD return a response with this status code if a request included a Range request-header field (section 14.35), and none of the range-specifier values in this field overlap the current extent of the selected resource, and the request did not include an If-Range request-header field. (For byte-ranges, this means that the first- byte-pos of all of the byte-range-spec values were greater than the current length of the selected resource.)</p>
<p>When this status code is returned for a byte-range request, the response SHOULD include a Content-Range entity-header field specifying the current length of the selected resource (see section 14.16). This response MUST NOT use the multipart/byteranges content- type.</p>
<h3>Wikipedia</h3>
<p>The client has asked for a portion of the file, but the server cannot supply that portion. For example, if the client asked for a part of the file that lies beyond the end of the file.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#expectation_failed">417 Expectation Failed</a>
<div id="expectation_failed" class="collapse">
<p>The expectation given in an Expect request-header field (see section 14.20) could not be met by this server, or, if the server is a proxy, the server has unambiguous evidence that the request could not be met by the next-hop server.</p>
<h3>Wikipedia</h3>
<p>The server cannot meet the requirements of the Expect request-header field.</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<a data-toggle="collapse" data-target="#teapot">418 I'm a teapot (RFC 2324)</a>
<div id="teapot" class="collapse">
<h3>Wikipedia</h3>
<p>This code was defined in 1998 as one of the traditional IETF April Fools' jokes, in RFC 2324, Hyper Text Coffee Pot Control Protocol, and is not expected to be implemented by actual HTTP servers. However, known implementations do exist. An Nginx HTTP server uses this code to simulate goto-like behaviour in its configuration.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#enhance_your_calm">420 Enhance Your Calm (Twitter)</a>
<div id="enhance_your_calm" class="collapse">
<h3>Wikipedia</h3>
<p>Returned by the Twitter Search and Trends API when the client is being rate limited. The text is a quote from 'Demolition Man' and the '420' code is likely a reference to this number's association with marijuana. Other services may wish to implement the 429 Too Many Requests response code instead.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#unprocessable_entity">422 Unprocessable Entity (WebDAV)</a>
<div id="unprocessable_entity" class="collapse">
<p>The 422 (Unprocessable Entity) status code means the server understands the content type of the request entity (hence a 415(Unsupported Media Type) status code is inappropriate), and the syntax of the request entity is correct (thus a 400 (Bad Request) status code is inappropriate) but was unable to process the contained instructions.  For example, this error condition may occur if an XML request body contains well-formed (i.e., syntactically correct), but semantically erroneous, XML instructions.</p>
<h3>Wikipedia</h3>
<p>The request was well-formed but was unable to be followed due to semantic errors.</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<a data-toggle="collapse" data-target="#locked">423 Locked (WebDAV)</a>
<div id="locked" class="collapse">
<p>The 423 (Locked) status code means the source or destination resource of a method is locked.  This response SHOULD contain an appropriate precondition or postcondition code, such as 'lock-token-submitted' or 'no-conflicting-lock'.</p>
<h3>Wikipedia</h3>
<p>The resource that is being accessed is locked.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#failed_dependency">424 Failed Dependency (WebDAV)</a>
<div id="failed_dependency" class="collapse">
<p>The 424 (Failed Dependency) status code means that the method could not be performed on the resource because the requested action depended on another action and that action failed.  For example, if a command in a PROPPATCH method fails, then, at minimum, the rest of the commands will also fail with 424 (Failed Dependency).</p>
<h3>Wikipedia</h3>
<p>The request failed due to failure of a previous request (e.g. a PROPPATCH).</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#adv_collections_expired">425 Reserved for WebDAV</a>
<div id="adv_collections_expired" class="collapse">
<p>Slein, J., Whitehead, E.J., et al., &quot;WebDAV Advanced Collections Protocol&quot;,  Work In Progress.</p>
<h3>Wikipedia</h3>
<p>Defined in drafts of &quot;WebDAV Advanced Collections Protocol&quot;, but not present in &quot;Web Distributed Authoring and Versioning (WebDAV) Ordered Collections Protocol&quot;.</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<a data-toggle="collapse" data-target="#upgrade_required">426 Upgrade Required</a>
<div id="upgrade_required" class="collapse">
<p>Reliable, interoperable negotiation of Upgrade features requires an unambiguous failure signal. The 426 Upgrade Required status code allows a server to definitively state the precise protocol extensions a given resource must be served with.</p>
<h3>Wikipedia</h3>
<p>The client should switch to a different protocol such as TLS/1.0.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#precondition_required">428 Precondition Required</a>
<div id="precondition_required" class="collapse">
<p>The 428 status code indicates that the origin server requires the request to be conditional.</p>
<p>Its typical use is to avoid the "lost update" problem, where a client GETs a resource's state, modifies it, and PUTs it back to the server, when meanwhile a third party has modified the state on the server, leading to a conflict.  By requiring requests to be conditional, the server can assure that clients are working with the correct copies.</p>
<p>Responses using this status code SHOULD explain how to resubmit the request successfully.</p>
<p>The 428 status code is optional; clients cannot rely upon its use to prevent &quot;lost update&quot; conflicts.</p>
<h3>Wikipedia</h3>
<p>The origin server requires the request to be conditional. Intended to prevent "the &quot;lost update&quot; problem, where a client GETs a resource's state, modifies it, and PUTs it back to the server, when meanwhile a third party has modified the state on the server, leading to a conflict.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#too_many_requests">429 Too Many Requests</a>
<div id="too_many_requests" class="collapse">
<p>The 429 status code indicates that the user has sent too many requests in a given amount of time ("rate limiting").</p>
<p>The response representations SHOULD include details explaining the condition, and MAY include a Retry-After header indicating how long to wait before making a new request.</p>
<p>When a server is under attack or just receiving a very large number of requests from a single party, responding to each with a 429 status code will consume resources.</p>
<p>Therefore, servers are not required to use the 429 status code; when limiting resource usage, it may be more appropriate to just drop connections, or take other steps.</p>
<h3>Wikipedia</h3>
<p>The user has sent too many requests in a given amount of time. Intended for use with rate limiting schemes.</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<a data-toggle="collapse" data-target="#request_header_fields_too_large">431 Request Header Fields Too Large</a>
<div id="request_header_fields_too_large" class="collapse">
<p>The 431 status code indicates that the server is unwilling to process the request because its header fields are too large.  The request MAY be resubmitted after reducing the size of the request header fields.</p>
<p>It can be used both when the set of request header fields in total are too large, and when a single header field is at fault.  In the latter case, the response representation SHOULD specify which header field was too large.</p>
<p>Servers are not required to use the 431 status code; when under attack, it may be more appropriate to just drop connections, or take other steps.</p>
<h3>Wikipedia</h3>
<p>The server is unwilling to process the request because either an individual header field, or all the header fields collectively, are too large.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#no_response_nginx">444 No Response (Nginx)</a>
<div id="no_response_nginx" class="collapse">
<h3>Wikipedia</h3>
<p>An Nginx HTTP server extension. The server returns no information to the client and closes the connection (useful as a deterrent for malware).</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#retry_with">449 Retry With (Microsoft)</a>
<div id="retry_with" class="collapse">
<h3>Wikipedia</h3>
<p>A Microsoft extension. The request should be retried after performing the appropriate action.</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<a data-toggle="collapse" data-target="#blocked_by_windows_parental">450 Blocked by Windows Parental Controls (Microsoft)</a>
<div id="blocked_by_windows_parental" class="collapse">
<h3>Wikipedia</h3>
<p>A Microsoft extension. This error is given when Windows Parental Controls are turned on and are blocking access to the given webpage.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#unavailable_for_legal_reasons">451 Unavailable For Legal Reasons</a>
<div id="unavailable_for_legal_reasons" class="collapse">
<h3>Wikipedia</h3>
<p>Intended to be used when resource access is denied for legal reasons, e.g. censorship or government-mandated blocked access. A reference to the 1953 dystopian novel Fahrenheit 451, where books are outlawed, and the autoignition temperature of paper, 451°F.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#client_closed_request">499 Client Closed Request (Nginx)</a>
<div id="client_closed_request" class="collapse">
<h3>Wikipedia</h3>
<p>An Nginx HTTP server extension. This code is introduced to log the case when the connection is closed by client while HTTP server is processing its request, making server unable to send the HTTP header back.</p>


## 5xx Server Error
The server failed to fulfill an apparently valid request.

Response status codes beginning with the digit "5" indicate cases in which the server is aware that it has erred or is incapable of performing the request. Except when responding to a `HEAD` request, the server SHOULD include an entity containing an explanation of the error situation, and whether it is a temporary or permanent condition. Clients SHOULD display any included entity to the user. These response codes are applicable to any request method.

<i class="icon-star"></i> <a data-toggle="collapse" data-target="#internal_server_error">500 Internal Server Error</a>
<div id="internal_server_error" class="collapse">
<p>The server encountered an unexpected condition which prevented it from fulfilling the request.</p>
<h3>Wikipedia</h3>
<p>A generic error message, given when no more specific message is suitable.</p>
<p><i class="icon-star"></i> The general catch-all error when the server-side throws an exception.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#not_implemented">501 Not Implemented</a>
<div id="not_implemented" class="collapse">
<p>The server does not support the functionality required to fulfill the request. This is the appropriate response when the server does not recognize the request method and is not capable of supporting it for any resource.</p>
<h3>Wikipedia</h3>
<p>The server either does not recognise the request method, or it lacks the ability to fulfill the request.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#bad_gateway">502 Bad Gateway</a>
<div id="bad_gateway" class="collapse">
<p>The server, while acting as a gateway or proxy, received an invalid response from the upstream server it accessed in attempting to fulfill the request.</p>
<h3>Wikipedia</h3>
<p>The server was acting as a gateway or proxy and received an invalid response from the upstream server.</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<a data-toggle="collapse" data-target="#service_unavailable">503 Service Unavailable</a>
<div id="service_unavailable" class="collapse">
<p>The server is currently unable to handle the request due to a temporary overloading or maintenance of the server. The implication is that this is a temporary condition which will be alleviated after some delay. If known, the length of the delay MAY be indicated in a Retry-After header. If no Retry-After is given, the client SHOULD handle the response as it would for a 500 response.</p>
<blockquote>Note: The existence of the 503 status code does not imply that a server must use it when becoming overloaded. Some servers may wish to simply refuse the connection.</blockquote>
<h3>Wikipedia</h3>
<p>The server is currently unavailable (because it is overloaded or down for maintenance). Generally, this is a temporary state.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#gateway_timeout">504 Gateway Timeout</a>
<div id="gateway_timeout" class="collapse">
<p>The server, while acting as a gateway or proxy, did not receive a timely response from the upstream server specified by the URI (e.g. HTTP, FTP, LDAP) or some other auxiliary server (e.g. DNS) it needed to access in attempting to complete the request.</p>
<blockquote>Note: Note to implementors: some deployed proxies are known to return 400 or 500 when DNS lookups time out.</blockquote>
<h3>Wikipedia</h3>
<p>The server was acting as a gateway or proxy and did not receive a timely response from the upstream server.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#version_not_supported">505 HTTP Version Not Supported</a>
<div id="version_not_supported" class="collapse">
<p>The server does not support, or refuses to support, the HTTP protocol version that was used in the request message. The server is indicating that it is unable or unwilling to complete the request using the same major version as the client, as described in section 3.1, other than with this error message. The response SHOULD contain an entity describing why that version is not supported and what other protocols are supported by that server.</p>
<h3>Wikipedia</h3>
<p>The server does not support the HTTP protocol version used in the request.</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<a data-toggle="collapse" data-target="#variant_also_negotiates">506 Variant Also Negotiates (Experimental)</a>
<div id="variant_also_negotiates" class="collapse">
<p>The 506 status code indicates that the server has an internal configuration error: the chosen variant resource is configured to engage in transparent content negotiation itself, and is therefore not a proper end point in the negotiation process.</p>
<h3>Wikipedia</h3>
<p>Transparent content negotiation for the request results in a circular reference.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#insufficient_storage">507 Insufficient Storage (WebDAV)</a>
<div id="insufficient_storage" class="collapse">
<p>The 507 (Insufficient Storage) status code means the method could not be performed on the resource because the server is unable to store the representation needed to successfully complete the request.  This condition is considered to be temporary.  If the request that received this status code was the result of a user action, the request MUST NOT be repeated until it is requested by a separate user action.</p>
<h3>Wikipedia</h3>
<p>The server is unable to store the representation needed to complete the request.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#loop_detected">508 Loop Detected (WebDAV)</a>
<div id="loop_detected" class="collapse">
<p>The 508 (Loop Detected) status code indicates that the server terminated an operation because it encountered an infinite loop while processing a request with &quot;Depth: infinity&quot;.  This status indicates that the entire operation failed.</p>
<h3>Wikipedia</h3>
<p>The server detected an infinite loop while processing the request (sent in lieu of 208).</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<a data-toggle="collapse" data-target="#bandwidth_limit_exceeded">509 Bandwidth Limit Exceeded (Apache)</a>
<div id="bandwidth_limit_exceeded" class="collapse">
<h3>Wikipedia</h3>
<p>This status code, while used by many servers, is not specified in any RFCs.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#not_extended">510 Not Extended</a>
<div id="not_extended" class="collapse">
<p>The policy for accessing the resource has not been met in the request.  The server should send back all the information necessary for the client to issue an extended request. It is outside the scope of this specification to specify how the extensions inform the client.</p>
<p>If the 510 response contains information about extensions that were not present in the initial request then the client MAY repeat the request if it has reason to believe it can fulfill the extension policy by modifying the request according to the information provided in the 510 response. Otherwise the client MAY present any entity included in the 510 response to the user, since that entity may include relevant diagnostic information.</p>
<h3>Wikipedia</h3>
<p>Further extensions to the request are required for the server to fulfill it.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#net_authn_required">511 Network Authentication Required</a>
<div id="net_authn_required" class="collapse">
<p>The 511 status code indicates that the client needs to authenticate to gain network access.</p>
<p>The response representation SHOULD contain a link to a resource that allows the user to submit credentials (e.g. with a HTML form).</p>
<p>Note that the 511 response SHOULD NOT contain a challenge or the login interface itself, because browsers would show the login interface as being associated with the originally requested URL, which may cause confusion.</p>
<p>The 511 status SHOULD NOT be generated by origin servers; it is intended for use by intercepting proxies that are interposed as a means of controlling access to the network.</p>
<p>Responses with the 511 status code MUST NOT be stored by a cache.</p>
<p>The 511 status code is designed to mitigate problems caused by &quot;captive portals&quot; to software (especially non-browser agents) that is expecting a response from the server that a request was made to, not the intervening network infrastructure.  It is not intended to encouraged deployment of captive portals, only to limit the damage caused by them.</p>
<p>A network operator wishing to require some authentication, acceptance of terms or other user interaction before granting access usually does so by identifing clients who have not done so (&quot;unknown clients&quot;) using their MAC addresses.</p>
<p>Unknown clients then have all traffic blocked, except for that on TCP port 80, which is sent to a HTTP server (the &quot;login server&quot;) dedicated to &quot;logging in&quot; unknown clients, and of course traffic to the login server itself.</p>
<p>In common use, a response carrying the 511 status code will not come from the origin server indicated in the request's URL.  This presents many security issues; e.g., an attacking intermediary may be inserting cookies into the original domain's name space, may be observing cookies or HTTP authentication credentials sent from the user agent, and so on.</p>
<p>However, these risks are not unique to the 511 status code; in other words, a captive portal that is not using this status code introduces the same issues.</p>
<p>Also, note that captive portals using this status code on an SSL or TLS connection (commonly, port 443) will generate a certificate error on the client.</p>
<h3>Wikipedia</h3>
<p>The client needs to authenticate to gain network access. Intended for use by intercepting proxies used to control access to the network (e.g., &quot;captive portals&quot; used to require agreement to Terms of Service before granting full Internet access via a Wi-Fi hotspot).</p>
</div>
</div>
</div>
<div class="row">
<div class="span4">
<a data-toggle="collapse" data-target="#network_read_timeout">598 Network read timeout error</a>
<div id="network_read_timeout" class="collapse">
<h3>Wikipedia</h3>
<p>This status code is not specified in any RFCs, but is used by some HTTP proxies to signal a network read timeout behind the proxy to a client in front of the proxy.</p>
</div>
</div>
<div class="span4">
<a data-toggle="collapse" data-target="#network_connect_timeout">599 Network connect timeout error</a>
<div id="network_connect_timeout" class="collapse">
<h3>Wikipedia</h3>
<p>This status code is not specified in any RFCs, but is used by some HTTP proxies to signal a network connect timeout behind the proxy to a client in front of the proxy.</p>


---

★ **"Top 10"** HTTP Status Code. More REST service-specific information is contained in the entry.