---
layout: post
title: "Jwt Authentication in .Net Core !"
date: 2020-05-03 10:22:41 +0500
categories: jwt auth net core
---

JWT (Json Web Token), are json based tokens that lets you give access to resources only to the authorized users. In order to incorporate, JWT in a .net core application: we have to first setup this into `ConfigureServices` method in the **Startup** class.
The first line defines the authentication schema to be used i.e. Basic/Bearer etc. via constant property `AuthenticationScheme` in `JwtBearerDefaults` class.
After setting up the authentication scheme, next we've to define validation parameters for the token. These parameters involves target audience, issue of the token, security key used for encryption and some flags to toggle validations as needed. The `AddJwtBearer` method takes a **JwtBearerOption** parameter which allows these flags to be setup and managed.

The **JwtBearerOption** contains 10's of other properties i.e. Events, Challenges, Audience, TokenValidationParameters etc. But for now, we'll keep it simple by using only the minimum possible properties to setup a working api. The **TokenValidationParameters** property lets us define the flags like Audience, Issuer and the Symmetric Key for the Token. `ValidateLifetime` field defines whether the token should've eternal life time or it has some time limit to accessed before authenticating a token.

```cs

    // add it to the 'ConfigureServices' method of the Startup.cs
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.TokenValidationParameters = new TokenValidationParameters
            {
                ValidateIssuer = true,
                ValidateAudience = false,
                ValidateLifetime = true,
                ValidIssuer = "ISSUE_NAME_HERE",
                ValidateIssuerSigningKey = true,
                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("YOUR_SECRET_KEY"))
            };
        });

    // add it the Configure method of Startup.cs
    application.UseAuthentication();
```

That setups the JWT for us, now for the next phase: we're going to implementing an api where we'll be using these JWTs.

```cs

    [ApiController]
    [Route("api/[controller]")]
    public class MoviesController : ControllerBase
    {
        [AllowAnonymous]
        [HttpPost(template: "token", Name = "tokenRequest")]
        public string Token(UserModel userInfo)
        {
            /*
                Validate User Here i.e.
                bool isValid = _usersRepo.Where(x => x.UserName == userInfo.UserName && x.Password == userInfo.Password).Exists();
            */
            var securityKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("YOUR_SECRET_KEY"));
            var credentials = new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            var token = new JwtSecurityToken("ISSUE_NAME_HERE",
              null,
              null,
              expires: DateTime.Now.AddMinutes(5),
              signingCredentials: credentials);
            return new JwtSecurityTokenHandler().WriteToken(token);
        }

        [HttpPost(template: "latest", Name = "latestMovies")]
        public List<Movie> Movies()
        {
            return new List<Movie>(){
                new Movie(){ Id = 1, Name = "ABC", Genre = "AAA", Rating  = 5 },
                new Movie(){ Id = 2, Name = "DEF", Genre = "BBB", Rating  = 4 },
                new Movie(){ Id = 3, Name = "GHI", Genre = "CCC", Rating  = 1 },
                new Movie(){ Id = 4, Name = "JKL", Genre = "DDD", Rating  = 3 }
            }
        }

    }
```

This api should work fine, if you're using it from with-in the application. But once you try it via CURL or an ajax request from a simple webpage. You might notice that you wouldn't be able to access these api endpoints. This's because your request's origin is not the same as that of the application. Web applications, as a security measures, server only same-origin request but in order to make them available for requests that are from other domain; we'll have to use CORS policies.

First, we're going to define a CORS policy only for that api and not for the whole applications. So, add the following snippet in the `ConfigureServices` method of Startup.cs. This setups ups a policy that allows request from any origin but only with GET, POST, and PUT requests.

```cs

    // add it to the 'ConfigureServices' method of the Startup.cs
    services.AddCors(options =>
    {
        options.AddPolicy(name: "POLICY_NAME_GOES_HERE",
            builder =>
            {
                builder.AllowAnyOrigin();
                builder.AllowAnyHeader();
                builder.WithHeaders(HeaderNames.ContentType);
                builder.WithMethods("PUT", "POST", "GET");
            });
    });

    // add it the Configure method of Startup.cs
    application.UseCors("POLICY_NAME_GOES_HERE");
```

Having placed the above code in Startup.cs for the CORS. Now, we apply it to our api controller. It's as simple as adding one more attribute to the api controller i.e. `[EnableCors("POLICY_NAME_GOES_HERE")]`. This makes it accessable now to everyone, who wants to interact with the api. Now, by default each endpoint is protected by a Token; So, we have to at least make one endpoint public that serves as an endpoint for accessing the Token. Here, in the api controller below; we're using `AllowAnonymous` on the `Token` endpoint for that.

```cs
    public class UserModel{
        public string UserName { get; set; }
        public string Password { get; set; }
    }
```

```cs
    public class Movie{
        public int Id { get; set; }
        public string Name { get; set; }
        public string Genre { get; set; }
        public int Rating { get; set; }
    }
```

Here's our api for the Movies; if you notice we've a method for Token and other method for fetching the list of movies.

```cs
    [ApiController]
    [Route("api/[controller]")]
    [EnableCors("POLICY_NAME_GOES_HERE")]
    public class MoviesController : ControllerBase
    {
        [AllowAnonymous]
        [HttpPost(template: "token", Name = "tokenRequest")]
        public string Token(UserModel userInfo)
        {
            /*
                Validate User Here i.e.
                bool isValid = _usersRepo.Where(x => x.UserName == userInfo.UserName && x.Password == userInfo.Password).Exists();
            */
            var securityKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("YOUR_SECRET_KEY"));
            var credentials = new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            var token = new JwtSecurityToken("ISSUE_NAME_HERE",
              null,
              null,
              expires: DateTime.Now.AddMinutes(5),
              signingCredentials: credentials);
            return new JwtSecurityTokenHandler().WriteToken(token);
        }

        [HttpPost(template: "latest", Name = "latestMovies")]
        public List<Movie> Movies()
        {
            return new List<Movie>(){
                new Movie(){ Id = 1, Name = "ABC", Genre = "AAA", Rating  = 5 },
                new Movie(){ Id = 2, Name = "DEF", Genre = "BBB", Rating  = 4 },
                new Movie(){ Id = 3, Name = "GHI", Genre = "CCC", Rating  = 1 },
                new Movie(){ Id = 4, Name = "JKL", Genre = "DDD", Rating  = 3 }
            }
        }

    }
```

That's sums up a small introduction for the JWT's. I hope this explanation would help to make a little understanding about api's and JWT.
