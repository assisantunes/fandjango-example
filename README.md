# Fandjango Example

This repository consists of a sample Facebook application powered by the [Fandjango](http://github.com/jgorset/fandjango) library
and a walkthrough to replicate it.

## Walkthrough

First of all, we'll need to register a new application with the Facebook platform. To do so,
go to [facebook.com/developers](http://www.facebook.com/developers) and create your application.
Once you have confirmed that you are in fact human, you'll be presented with a daunting number of
options. Don't worry, though; they're all optional and we only need to configure two of them right now.
Populate the "canvas page" field with the URL you'd like your application to be accessed from
(f.ex. 'http://apps.facebook.com/myapp') and the "canvas url" field with the URL that Facebook may
load your application from (f.ex. 'http://myserver.com/myapp'). You'll find both fields under
"Facebook integration".

Next, create a new Django project and install Fandjango:

    $ django-admin.py startproject myfacebookapplication
    $ pip install fandjango
    
Add fandjango to your list of installed applications, adjust your database settings and populate
it like you regularly would:

    $ vim settings.py
    $ ./manage.py syncdb

Fandjango needs some configuration. Specifically, you need to add its middleware to your middleware classes and specify
your application's id, secret key and canvas url (all of which can be found on [facebook.com/developers](http://www.facebook.com/developers))
in your settings file:

    FACEBOOK_APPLICATION_ID = 118434614903049
    FACEBOOK_SECRET_KEY = '8cce95c678d2cd3f51f11e7cce03cd21'
    FACEBOOK_APPLICATION_URL = 'http://apps.facebook.com/myapp'
    
    MIDDLEWARE_CLASSES = [
        'django.middleware.common.CommonMiddleware',
        'django.contrib.sessions.middleware.SessionMiddleware',
        'fandjango.middleware.FacebookMiddleware',
        'django.middleware.csrf.CsrfViewMiddleware',
        'django.contrib.auth.middleware.AuthenticationMiddleware',
        'django.contrib.messages.middleware.MessageMiddleware',
    ]
    
*Note:* If you're using Django's built-in CSRF protection middleware, you need to make sure Fandjango's middleware precedes it.
Otherwise, Facebook's requests to your application will qualify cross-site request forgeries.

Now define a view and route it:

    # urls.py
    
    urlpatterns = patterns('',
        # Examples:
        url(r'^$', 'fandjangoexample.views.home', name='home'),
    )
    
    # views.py
    
    from django.http import HttpResponse

    @facebook_authorization_required()
    def home(request):
        return HttpResponse('Hello, %s' % request.facebook.user.first_name)

That's it! You have created a Facebook application that authorizes and greets its users. Navigate to your application's canvas URL
to have a closer look at your new marvel of technology.
