# Web Application Life Cycle

> [!tip] History of Web Development
>
> - 1990 - 1996 => Static Web Pages (HTML + Text + Images)
> - 1996 => Dynamic Web Pages (HTML + CSS + JavaScript)
> - template page: page that will be created in runtime (different for each user) depending on the data
> - to deal with the data, we need a server (backend) to get the data and communicate with the database
> - 2010 => semantic web
> - gather data about the user and provide a better user experience (like ads based on user's search history)

> [!example] client-server architecture
>
> - client: browser
> - server: application that runs on a server
>   > [!faq] What is the difference between a server and a client?
>   >
>   > - server is always on and available for the internet
>   > - there are other services the internet can provide (like email, ...)
>   > - ip should be unique for each machine on the internet

> [!tip] Real IP vs Virtual IP
>
> - due to the increase of machines on the internet => using IPv6, which has 128 bits
> - other solution is to use NAT (Network Address Translation) => one real IP and many virtual IPs
> - can not reach the device through the virtual IP from the internet
> - Server MUST HAVE ONE unchanged real IP (to be able to reach it from the internet)
> - this is the biggest difference between a server and a client

> [!example] DNS (Domain Name System)
>
> - to reach a server, we need to know its IP address
> - DNS is a service that translates domain names to IP addresses
> - in the browser, we type the domain name, and the browser will ask the DNS to get the IP address of the domain name

> [!example] IIS tool
>
> - it is like Apache (Linux)
> - used to handle requests
> - used to host web applications on Windows
> - metabase:
> - when we request a file, IIS has 3 cases:
> - search in the metabase for the file
> - if not found, it will respond with a 404 error page
> - if found, 2 cases:
>
> 1. static file(html, css, js, ...) => IIS will return the file to the browser
> 2. any other file => IIS will call the component that will convert the file to html and return it to the browser
>
> - IIS needs to compile the C# code before sending it to the browser as browser can not understand C# code (.NET runtime)
> - `/mypage.asp` => IIS will call the ASP component to convert the file to html
> - browser interprets the html and is responsible for getting the css , js, images, ... (Browser interpreter)
> - it will send requests to the server to get the files (css, js, images, ...)
> - thats why on loading page on slow internet, we see the page without css, js, images, ... and then they appear one by one
> - to get the page that contain css + js + images,
> - it will send a request for the html file and then for each file (css, js, images, ...)

> [!done] techniques to improve the performance of the web application
>
> - minify the files (css, js, html, ...): remove the comments and spaces
> - bundle the files: combine all the files in one file
> - and for images => css sprites: combine all the icons/images in one image and request the image once
> - make div for each icon and set the background image to the sprite image and set the background position to the icon position
> - use any css sprite generator to generate the css code for the sprite image

> [!tip] Hosting
>
> - we can host static files
> - we can host the web application on local network
> - to make it available for the internet, we need to give it a real IP
> - IIS is on Windows by default we just need to enable it
> - from control panel => programs => turn windows features on or off => check on IIS(Internet Information Services)
> - can used as web server, ftp server, ...
> - to test it write `localhost` in the browser
> - using IIS manager
> - Application Pools: used to create the objects to control the requests
> - Sites: used to create the website
> - Add Website: to create a new website
> - application pool: choose the application pool that will handle the requests
> - port: used to differentiate between the websites on the same server or applications on the same machine (each application has a unique port)
> - will use port 80 by default
> - choose port between 1024 and 65535
> - we dont have to specify real IP or domain name

> [!error] UnAuthorized Access
>
> - `localhost:4444` => will give an error
> - can not use windows user security to access the website
> - IIS is working with 2 other users: IIS_IUSRS and IUSR
> - cannot open a file on IIS unless one of these users has access to the file
> - other solution is to put the website in the `wwwroot` folder in the `inetpub` folder

> [!done] 1. give permission to the folder
>
> - right click on the folder => properties => security => edit => add => `IIS_IUSRS`or `IUSR` or both =>
> - only read permission is enough

> [!done] 2. put the website in the `wwwroot` folder

> [!error] `localhost:4444` => will give an error
>
> - default document is not set
> - we have to set the default document to be opened when we open the website
> - we can specify multiple default documents and order them so that the first one that exists will be opened

> [!tip] to get the IP address of the machine
>
> - to access the website from another machine on the same network
> - go to the command prompt and write

```cmd
ipconfig
```

> [!warning] we have to enable this port in the firewall
>
> - go to control panel => windows defender firewall => advanced settings => inbound rules => new rule => port => next => choose the port => next => allow the connection => next => choose the profile => next => give it a name => finish
> - now we can access the website from another machine on the same network by writing the IP address of the machine and the port number

---

# Break

---

> [!warning] How to deal with database
>
> - we can host the database on the same machine as the web application
> - but we will use sql server authentication to host the database on another machine
> - windows authentication: in case it is on the same machine
> - sql server authentication: in case it is on another machine

> [!example] Host .NET Application
>
> - Download .NET 8.0 only the runtime (not the SDK)
> - ASP.NET Core 8.0 runtime
> - or we can download .NET Hosting Bundle (integration of .NET runtime and IIS)
> - [Download .NET Hosting Bundle]([https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/iis/hosting-bundle?view=aspnetcore-8.0)
> - this is used to run the .NET application on IIS
> - from control panel => programs => turn windows features on or off => check on IIS(Internet Information Services) => world wide web services => application development features => check on .NET Extensibility 4.8, ASP.NET 4.8, ISAPI Extensions, ISAPI Filters
> - make it first before installing hosting bundle to restart only once

![](Pasted%20image%2020240424110128.png)

> [!warning] How to create user in SQL Server and give it permission to the database
>
> - open SQL Server Management Studio
> - connect to the server
> - security => logins => new login
> - general => login name => password => confirm password
> - server roles => sysadmin
> - user mapping => choose the database => db_owner
> - ok
> - enable sql server authentication and windows authentication from the server properties

> [!tip] Connection String
>
> - instead of `integrated security=true` we will use `user id=username;password=123456`
> -
> - `data source=.;initial catalog=dbname;user id=username ;password=123456; TrustServerCertificate=True;`
> - make Visual Studio run as administrator
> - right click on the project => publish => folder => create a new folder => next => next => finish
> - there are other options like ftp, azure, ...
> - we can set folder location as `http://localhost:4444` the url to the folder
> - more preferable to specify the folder location locally using the file system (Browse)
> - now we specified the folder location, we can publish the project
> - build + publish should be successful
> - publish problem => run as administrator
> - build problem => check the code for errors
> - this will create a folder with the published files (dll, exe, ...)
> - move the folder to the `wwwroot` folder in the `inetpub` folder
> - now we can access the website from the browser
> - `localhost:4444` => will not open swagger as it is configured to be only in development mode
> - but if we request to get data `localhost:4444/api/students` => will return the data
> - we can open swagger by moving it from check in the `startup.cs` file
> - but any changes in the code will require ==republishing== the project
> - stop the site => stop it in the application pool
> - move the published folder to the `wwwroot` folder inside the `inetpub` folder 
> - start the service => start it in the application pool
>   > [!danger] Republishing after moving swagger caused error due to swagger security token
>  >
> > - ==Skip== this 


```csharp
 if (app.Environment.IsDevelopment())
 {
 }
     app.UseSwagger();
     app.UseSwaggerUI();
```


>[!summary] 


![](Pasted%20image%2020240424113540.png)

----
# Lab 

- host app on local IIS
- you can ==try== host client and api 

