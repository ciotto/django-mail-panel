
Django Debug Toolbar - Mail Panel
===============================

![Screeny](https://cloud.githubusercontent.com/assets/1790447/9289964/6aa7c4ba-434e-11e5-8594-3bb3efd0cd81.png)


Testing and debugging emails while developing a django app has never been pleasant.  Sending emails to a file-based backend requires a user to click through obtusely named files and does not provide a way to preview rendered html.  Sending email to a valid mailbox incurs a delay as the email is processed though a mail server, all the while adding clutter to a developer's inbox, and even occasionally finds itself buried in a spam directory.

The mail panel attempts to address these problems by providing a way to preview emails within the browser window.

This mail panel is released under the Apache license. If you like it, please consider contributing!

Special thanks to @ShawnMilo for the code review.


Installation
============

To install the mail panel, first install this package with `pip install django-debug-toolbar-mail`, then add mail_panel after debug_toolbar to INSTALLED_APPS setting:

    INSTALLED_APPS = (
        ...
        'debug_toolbar',
        'mail_panel',
    )

and add the panel DEBUG_TOOLBAR_PANELS:

    DEBUG_TOOLBAR_PANELS = (
        ...
        'mail_panel.panels.MailToolbarPanel',
    )


If you use the DEBUG_TOOLBAR_PANELS to custom order your panels:

    DEBUG_TOOLBAR_PANELS = [
        'debug_toolbar.panels.versions.VersionsPanel',
        'debug_toolbar.panels.timer.TimerPanel',
        'debug_toolbar.panels.settings.SettingsPanel',
        'debug_toolbar.panels.headers.HeadersPanel',
        'debug_toolbar.panels.request.RequestPanel',
        'debug_toolbar.panels.sql.SQLPanel',
        'debug_toolbar.panels.staticfiles.StaticFilesPanel',
        'debug_toolbar.panels.templates.TemplatesPanel',
        'debug_toolbar.panels.cache.CachePanel',
        'debug_toolbar.panels.signals.SignalsPanel',
        'debug_toolbar.panels.logging.LoggingPanel',
        'debug_toolbar.panels.redirects.RedirectsPanel',
        'mail_panel.panels.MailToolbarPanel',
    ]


Collect static and you'll be good to go.

    ./manage.py collectstatic


Configuration
=============

After installation, you now need to redirect mail to the mail toolbar.  Change your email backend to the following:

    EMAIL_BACKEND = 'mail_panel.backend.MailToolbarBackend'


By default, mail toolbar stores messages for one day before removing them from cache.  You can change this with the following setting:

    MAIL_TOOLBAR_TTL = 86400  # 1 Day



Testing
=======

To preview emails sent from your test suite, add the email backend override to your tests with the following:
from django.test.utils import override_settings

    @override_settings(EMAIL_BACKEND='mail_panel.backend.MailToolbarBackend')
    def test_send_email(self):
        ...


The backend works similarly to the standard email backend and code should not need to be reworked when using the MailToolbarBackend.

    from django.core import mail

    original_outbox = len(mail.outbox)
    # Send mail ...
    assert(len(mail.outbox) == original_outbox + 1)
