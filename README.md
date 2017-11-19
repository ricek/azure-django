# Polls

Polls is a simple Django app to conduct Web-based polls. For each question, visitors can choose between a fixed number of answers.

Detailed tutorial: [Writing your first Django app](https://docs.djangoproject.com/en/2.0/intro/).

## Hosting on Azure

1. [Setup continuous integration with Azure & GitHub](https://blogs.msdn.microsoft.com/microsoftimagine/2015/09/01/using-continuous-integration-with-azure-github/)
2. Install supported Python version for Django 2.x through Extensions
3. [Setting web.config to point to the Python interpreter](https://docs.microsoft.com/en-us/visualstudio/python/managing-python-on-azure-app-service)
4. Skip old auto-processing stuff for depolyment by adding `.skipPythonDeployment`
5. [Installing packages via Kudu console](https://docs.microsoft.com/en-us/visualstudio/python/managing-python-on-azure-app-service#azure-app-service-kudu-console)

Visit `https://<your-app-name>.azurewebsites.net/` 

## Extra

Deploying a Django app in production is much less simple than on localhost. With debug turned off Django won't handle static files for you any more. Thus, if you try to load your app's admin site you will receive 404 not found for all your css files, etc.

In your `settings.py` make sure you have `django.contrib.staticfiles` installed and modify your code as follow:

    DEBUG = False
    ALLOWED_HOSTS = ['<your-app-name>.azurewebsites.net']
    STATIC_URL = '/static/'
    STATIC_ROOT = os.path.join(BASE_DIR, 'static')

Run the command `python manage.py collectstatic` on Azure via Kudu or locally on your machine and then commit to your Github repo.

### Serving static files

Create `web.config` file with below content inside `static` folder

    <?xml version="1.0"?>  
    <configuration>  
        <system.webServer>
            <handlers>
                <clear />
                <add name="StaticFile"
                     path="*" verb="*" modules="StaticFileModule,DefaultDocumentModule,DirectoryListingModule" 
                     resourceType="Either" 
                     requireAccess="Read" />
            </handlers>
        </system.webServer>
    </configuration>

### Register WOFF files

Add below content to `<system.webServer>` in the `web.config` file in your root. You may also want to add a `mimeMap` for all the web-font extensions you use.

    <staticContent>
        <mimeMap fileExtension="woff" mimeType="application/font-woff" />
    </staticContent>
    
### Add home views

Since Django tutorial only covers `/polls` and `/admin` your home page will be 404 not found if you try to access `https://<your-app-name>.azurewebsites.net/`. Simply create another function view & template and add to `mysite/urls.py`
