![CommonPasswordsValidator logo](https://raw.githubusercontent.com/andrewlock/CommonPasswordsValidator/master/logo.png)
# CommonPasswordsValidator

[![Build status](https://ci.appveyor.com/api/projects/status/mqlpvis18ll4rj6f/branch/master?svg=true)](https://ci.appveyor.com/project/andrewlock/commonpasswordsvalidator/branch/master)
<!--[![Travis](https://img.shields.io/travis/andrewlock/CommonPasswordsValidator.svg?maxAge=3600&label=travis)](https://travis-ci.org/andrewlock/CommonPasswordsValidator)-->
[![NuGet](https://img.shields.io/nuget/v/CommonPasswordsValidator.svg)](https://www.nuget.org/packages/CommonPasswordsValidator/)
[![MyGet CI](https://img.shields.io/myget/andrewlock-ci/v/CommonPasswordsValidator.svg)](http://myget.org/gallery/acndrewlock-ci)

Implementations of [ASP.NET Core Identity](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity) `IPasswordValidators` that verify the provided password is not one of the most common passwords.

## Why should you care?

Password rules are a pain, and users hate them! Worse than that - even though they make the password mathematically stronger, the real world benefit is questionable.

> Changing the ones to 'I' does not a strong password make!

This package lets you relax those rules in favour of pure length, and requiring that passwords are not one of the top 100, top 1000, or even top 100,000  most common passwords.

## Background 

This package is based [on an article by Jeff Attwood](https://blog.codinghorror.com/password-rules-are-bullshit/) about the rules they have decided on for [Discource](https://discourse.org/). 

Instead of requiring a multitude of character types, they demand a _minimum_ of 10 characters and at least 6 unque characters.

More importantly, they require that the password is not one of the most common passwords. 

This package provides a number of validators for the ASP.NET Core Identity system, that you can use in your ASP.NET Core 2.0 apps to check that the password entered is not on a list of the most common passwords. 


>*NOTE* This package is currently for ASP.NET Core Identity 2.0-preview-2, so requires .NET Core 2.0-preview2 is installed.

## Installing 

Install using the [CommonPasswordsValidator NuGet package](https://www.nuget.org/packages/CommonPasswordsValidator) (currently in beta):

```
PM> Install-Package CommonPasswordsValidator -Pre
```

## Usage 

When you install the package, it should be added to your `csproj`. Alternatively, you can add it directly by adding:


```xml
<PackageReference Include="NetEscapades.CommonPasswordValidator" Version="0.1.0-*" />
```

You can add the password validator to you ASP.NET Core Identity configuration using one of the extension methods. 

Extension methods exist for validating wether the password is in the top 

* 100 most common of the the 10 million password list
* 500 most common of the the 10 million password list
* 1,000 most common of the the 10 million password list
* 10,000 most common of the the 10 million password list
* 100,000 most common of the the 10 million password list

For example, to add the top 1000 password validator to a typical defulat ASP.NET Core project:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders()
        .AddTop1000PasswordValidator<ApplicationUser>(); // Add the custom validator

    services.AddTransient<IEmailSender, AuthMessageSender>();
    services.AddTransient<ISmsSender, AuthMessageSender>();

    services.AddMvc();
}
```

In adition, an extension method exists to quickly remove the character password requirements, and use length requirements only

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>(options =>
            options.UseLengthOnlyOptions(  // configure the default options
                requiredLength: 10,
                requiredUniqueChars: 6
            ))
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders()
        .AddTop100000PasswordValidator<ApplicationUser>();

    services.AddTransient<IEmailSender, AuthMessageSender>();
    services.AddTransient<ISmsSender, AuthMessageSender>();

    services.AddMvc();
}
```

## Additional Resources
* [Password Rules Are Bullshit](https://blog.codinghorror.com/password-rules-are-bullshit/)
* [Source passwords list](https://github.com/danielmiessler/SecLists/tree/master/Passwords)
* [Introduction to ASP.NET Core Identity](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity)