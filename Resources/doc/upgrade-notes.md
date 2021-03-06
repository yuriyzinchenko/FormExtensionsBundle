# Upgrade notes
----------------------------------------------------

This file lists B/C breaking PRs in reverse chronological order. Each PR contains 
description explaining nature of changes and upgrade notes to help you upgrade your 
project.

## Commit [xxx][xxx]

[xxx]: https://github.com/symfony2admingenerator/FormExtensions/commit/xxx

#### Description:

**Changed bundle name and moved some code to a seperate bundle**: The bundle has been renamed and the twig extension has been moved to a seperate bundle. You need to register fix your `AppKernel.php`.

**Changed twig block names**: The twig extension block names have changed, you need to update your templates:

* `s2a_form_stylesheet` was renamed to `form_css`
* `s2a_form_javascript` was renamed to `form_js`

**Changed form type prefix**: The FormExtensions have been moved under `symfony2admingenerator` github organization. As such, all form types will be prefixed with `s2a_`.

**Bundled assets in assetic packages**: This Commit bundled assets into assetic packages.

**Changed assetic filters**: the YUI compressor is no longer used. Instead uglifycss and uglifyjs is used to minify assets.

[form-bundle]: https://github.com/symfony2admingenerator/FormBundle

#### BC Break:

##### Changed bundle name and moved some code to a seperate bundle

The bundle has been renamed to "AdmingeneratorFormExtensionsBundle" and the Twig extension for form stylesheet and javascript blocks was moved to a [seperate bundle][form-bundle]. The dependency was added to composer.json, so it should auto-download upon update, however you need to update your `AppKernel.php`:

```php
<?php
// AppKernel.php
public function registerBundles()
{
    $bundles = array(
        // add these:
        new Admingenerator\FormBundle\AdmingeneratorFormBundle(),
        new Admingenerator\FormExtensionsBundle\AdmingeneratorFormExtensionsBundle(),
        // remove this:
        // new Avocode\FormExtensionsBundle\AdmingeneratorFormExtensionsBundle(),
    );
}
?>
```

##### Changed twig block names

The block names have changed, and in your base template you must rename them, as in:

For Admingenerator users:

```html+django
{% extends 'AdmingeneratorGeneratorBundle::base_admin.html.twig' %}

{% block stylesheets %}
    {{ parent() }}

    {% include 'AdmingeneratorFormExtensionsBundle::stylesheets.html.twig' %}
{% endblock %}

{% block javascripts %}
    {{ parent() }}

    {% include 'AdmingeneratorFormExtensionsBundle::javascripts.html.twig' %}
{% endblock %}
```

For others:

```html+django
{% block stylesheets %}
    {% include 'AdmingeneratorFormExtensionsBundle::stylesheets.html.twig' %}
    
    {% if form is defined and form is not empty %}
        {{ form_css(form) }}
    {% endif %}
{% endblock %}

{% block javascripts %}
    {% include 'AdmingeneratorFormExtensionsBundle::javascripts.html.twig' %}
    
    {% if form is defined and form is not empty %}
        {{ form_js(form) }}
    {% endif %}
{% endblock %}
```

##### Changed form type prefix

Form type names changed: `afe_` prefix is replaced by `s2a_`.

For assetic users, the CSS and JS assets have been bundled in packages named:
* formextensions_css
* formextensions_js


##### Bundled assets in assetic packages

You have to add packages configuration, to your `config.yml`:

```yaml
framework:
    // ...
    templating:
        packages:
             formextensions_css:
                version: 1.0
                version_format: "%%1$s?v%%2$s"  # use whatever format suits you
                                                # the %%1$s represents asset output path
                                                # the %%2$s represents version number
                                                # this format will output 'acme/demo.css'
                                                # as 'acme/demo.css?v1'
             formextensions_js:
                version: 1.0
                version_format: "%%1$s?v%%2$s"
```

##### Changed assetic filters

For assetic users, the CSS and JS filters have been changed to:
* uglifycss
* uglifyjs2

The cssrewrite filter is still used.

You have to add filters configuration to your `config.yml`:

```yaml
assetic:
    filters:
        cssrewrite: ~
        uglifycss:
            bin: /usr/bin/uglifycss # replace this with your path to uglifycss
        uglifyjs2:
            bin: /usr/bin/uglifyjs  # replace this with your path to uglifyjs
```
