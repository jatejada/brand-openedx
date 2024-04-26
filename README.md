# Open edX Brand Package Interface

This project contains the default branding assets and style used in Open edX applications. It is published on npm as `@edx/brand-openedx`.

The file structure serves as an interface to be implemented for custom branding and theming of Open edX.


## Change MFE

MicroFrontEnd Base plugin for tutor comes with a list of mfes: 
(Account, Gradebook, Learning, Profile, Course authoring, discussions and authn), in 
order to change those parts we need to apply patches in the mfe plugin.

-Why is it hard to change mfe styles? :
https://open-edx-proposals.readthedocs.io/en/latest/architectural-decisions/oep-0048-brand-customization.html

Here we learn about the brand customization packages which include the necessary
files to overwrite the styles in the mfe’s.

## Creating and modifying a tutor plugin

We’ll need to create a plugin which will be consumed by the mfe plugin itself and it’ll 
work as a patch: (Let’s call it myplugin.py)

1. $ mkdir -p "$(tutor plugins printroot)" – Create the plugins folder

2. $ touch "$(tutor plugins printroot)/myplugin.py" – Create the plugin 
file in the folder.

3. tutor plugins list – Verify it was created.

4. tutor plugins enable myplugin – Enable the plugin.

5. tutor config save – Regenerate the environment.

In order to modify it use (nano myplugin.py) making sure you are in the plugins folder with (cd "$(tutor plugins printroot)").

The content of the plugin that we just created should be something like this:

```
    from tutor import hooks
        hooks.Filters.ENV_PATCHES.add_item(
            (
                "mfe-dockerfile-post-npm-install",
                """
                RUN npm install '@edx/brand@git+git@github.com:jatejada/custom-brand-openedx.git' --force
                """
            )
        )
```

```
    from tutor import hooks
        hooks.Filters.ENV_PATCHES.add_item(
            (
                "mfe-dockerfile-post-npm-install",
                """
                RUN npm install '@edx/brand-openedx@git+https://github.com/jatejada/brand-openedx' --force
                """
            )
        )
```

## Make changes on the mfe

1. Update the repository files.

2. In the server run the command tutor config save.

3. Run tutor images build --no-cache mfe.

4. Run tutor local launch.