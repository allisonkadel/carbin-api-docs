# Errors

The Carbon Insights API uses the following error codes:


Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is invalid.
401 | Unauthorized -- Your JWT Token is incorrect.
405 | Method Not Allowed -- You tried to access an endpoint with an invalid method.
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.
429 | Too Many Requests -- You've exceeded the request limit for your account.

<!-- 403 | Forbidden -- The kitten requested is hidden for administrators only. -->
<!-- 404 | Not Found -- The specified resource could not be found. -->
<!-- 406 | Not Acceptable -- You requested a format that isn't json. -->
<!-- 410 | Gone -- The kitten requested has been removed from our servers. -->
<!-- 418 | I'm a teapot. -->
