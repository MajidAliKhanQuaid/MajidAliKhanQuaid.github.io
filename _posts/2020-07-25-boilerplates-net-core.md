---
layout: post
title: "Boilerplates Net Core !"
date: 2020-07-25 10:22:41 +0500
categories: net core jwt cors 
---

Template for adding Jwt Support and Entity Framework Core

```cs
	public class Startup
    {
        private readonly IConfiguration _configuration;
        public Startup(IConfiguration configuration)
        {
            _configuration = configuration;
        }

        public void ConfigureServices(IServiceCollection services)
        {
            var jwtConfig = _configuration.GetSection("Jwt");

            services.AddControllersWithViews();

            services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddJwtBearer(options =>
                {
                    options.TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = true,
                        ValidateAudience = false,
                        ValidateLifetime = true,
                        ValidIssuer = jwtConfig.GetValue<string>("IssuerName"),
                        ValidateIssuerSigningKey = true,
                        IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(jwtConfig.GetValue<string>("SecretKey")))
                    };
                });

            services.AddDbContext<ECodeShopContext>(options =>
                    options.UseSqlServer(_configuration.GetConnectionString("Default")));
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseRouting();
            app.UseAuthentication();
            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllers();
            });
        }
    }
```

Template for Creating DbContext

```cs
	public class ECodeShopContext : DbContext
    {
        public DbSet<User> Users { get; set; }
        public ECodeShopContext(DbContextOptions options) : base(options) { }
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<User>().HasData(new User() { Id = -1, Email = "user@ds.com", UserName = "ds", Password="1234",Phone = "0092 123 123", CreatedOnUtc = DateTime.UtcNow, UpdatedOnUtc = DateTime.UtcNow});
        }
    }
```

Packages

```ps
	Install-Package Microsoft.EntityFrameworkCore.Tools
	Install-Package Microsoft.EntityFrameworkCore.SqlServer
```