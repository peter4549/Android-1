# [How to POST raw whole JSON in the body of a Retrofit request?](https://stackoverflow.com/questions/21398598/how-to-post-raw-whole-json-in-the-body-of-a-retrofit-request)
## https://stackoverflow.com/a/64998109/15002852

you need to set `@Body` in interface
```
@Headers({ "Content-Type: application/json;charset=UTF-8"})
    @POST("Auth/Login")
    Call<ApiResponse> loginWithPhone(@Body HashMap<String, String> fields);
```
