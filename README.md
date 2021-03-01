# Interceptor

Please Enable [Retrofit](https://square.github.io/retrofit/) on your project, follow this step and [OkHttp](https://square.github.io/okhttp/interceptors/) on your project, follow this step.

- Add dependencies
```gradle
    implementation 'com.squareup.retrofit2:retrofit:2.6.0'
    implementation 'com.squareup.retrofit2:converter-gson:2.5.0'
    implementation 'com.squareup.okhttp3:logging-interceptor:3.10.0'
```

- Make Class `TokenInterceptor`
```java
public class TokenInterceptor {

    public TokenInterceptor() {
    }

    public OkHttpClient getClient(){
        HttpLoggingInterceptor interceptor = new HttpLoggingInterceptor();
        interceptor.setLevel(HttpLoggingInterceptor.Level.BODY);
        return new OkHttpClient
                .Builder()
                .addInterceptor(interceptor)
                .addInterceptor(new Interceptor() {
                    @Override
                    public Response intercept(Chain chain) throws IOException {
                        Request request = chain.request().newBuilder()
                                .addHeader("Accept", "application/json")
                                .addHeader("Authorization", "Bearer Token")
                                .build();
                        return chain.proceed(request);
                    }
                })
                .readTimeout(90, TimeUnit.SECONDS)
                .writeTimeout(90, TimeUnit.SECONDS)
                .connectTimeout(90, TimeUnit.SECONDS)
                .retryOnConnectionFailure(false)
                .cache(null)
                .build();
    }
}
```

- Add configuration to RetrofServer
```java
public class RetroServer {
    private static final String base_url = "http://192.168.0.121/retrofit/";

    private static Retrofit setInit(){
        OkHttpClient client = new TokenInterceptor().getClient();
        return new Retrofit.Builder()
                .baseUrl(base_url)
                .addConverterFactory(GsonConverterFactory.create())
                .client(client)
                .build();
    }

    public static ApiService getInstance(){
        return setInit().create(ApiService.class);
    }
}

public interface ApiService {
}
```

---

```
Copyright 2020 M. Fadli Zein
```
