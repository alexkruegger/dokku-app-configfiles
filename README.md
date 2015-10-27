dokku-app-configfiles
=====================

Rewritten version of [dokku-app-configfiles][2] is a plugin that allows [dokku][1] app config files (such
as `VHOST`, `ENV`, `DOCKER_OPTIONS`, `nginx.tpl` or `nginx.ssl.tpl`, etc. to be
taken from the app's git repository. This eliminates the need to manually create
these files on the dokku server.

In short, per-app dokku config files are checked into the app's git repository
under the directory `.dokku-config/`. Upon git push to the dokku server, any files in
`.dokku-config/*` are copied to `/home/dokku/[app name]/`.

* [1] https://github.com/progrium/dokku
* [2] https://github.com/mikexstudios/dokku-app-configfiles

## Requirements

* Dokku 0.3.17+ (for the `docker-options` plugin)

Changes from original:
---------------------

- rename hook 'pre-build' to 'pre-build-buildstep' / ln to 'pre-build-dockerfile'
(https://github.com/mikexstudios/dokku-app-configfiles/pull/6)
- remove the temporary container used to find the config files
(https://github.com/mikexstudios/dokku-app-configfiles/pull/5)
- rename config directory from '.dokku' -> '.dokku-config'

Why would I use this? (original)
---------------------

Although it is prudent to always keep server configuration files separate from
app files, many cloud hosts (such as dotcloud) offer the option to supply
custom ngnix and other configuration files by checking them into your app's 
root directory. This approach is simple, and an advantage is that these
configuration files are always backed up with the app. This plugin enables
similar functionality for dokku.


Installation and Usage
----------------------

1. Install the plugin.  On dokku 0.4+:
    ```sh
    sudo dokku plugin:install https://github.com/alexkruegger/dokku-app-configfiles.git
    ````
    On dokku 0.3.x, clone into the dokku plugins directory:
    ```sh
    git clone https://github.com/alexkruegger/dokku-app-configfiles.git /var/lib/dokku/plugins/dokku-app-configfiles
    ```

2. In your app's git repository (this is the app that you intend to push to
   dokku), create a folder called `.dokku-config`. For example, if my app was in a folder
   called `my-webapp/`, the folder would be created at `my-webapp/.dokku-config/`.
    ```sh
    cd my-webapp/ #this is the app you want to git push to dokku
    mkdir .dokku-config
    ```

3. Inside the `.dokku-config/` folder, place your dokku config files. For example, let's
   create some fixed environment variables:
    ```sh
    cd my-webapp/.dokku-config/ 
    echo "export HELLO='world'" > ENV
    echo "--name mywebapp" > DOCKER_OPTIONS_DEPLOY
    ```
    NOTE: In practice, you may not want to check sensitive environment variables
    (such as API keys) into your git repository if it is publically accessible.

4. Add the `.dokku-config/` directory to your app's git repository:
    ```sh
    cd my-webapp/ #this is the app you want to git push to dokku
    git add .dokku-config/
    git commit -m 'Added dokku config files.'
    ```

5. Push your app to dokku and the config files in `.dokku-config/*` should be copied to
   your app's directory (e.g. `/home/dokku/my-webapp/`) on the dokku server.

## Contributing

Feel free to fork and create a Pull Request.

License
-------

The MIT License (MIT)
