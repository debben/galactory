=======================
galactory Release Notes
=======================

.. contents:: Topics


v0.11.1
=======

Bugfixes
--------

- Requests for collections that were not already present in artifactory resulted in a 500 internal server error (https://github.com/briantist/galactory/issues/112, https://github.com/briantist/galactory/pull/116).
- Requests proxied to a v2 upstream endpoint that supports pagination caused a 400 error from the upstream due to the inclusion of the v3 ``limit`` query string parameter (https://github.com/briantist/galactory/issues/113).

v0.11.0
=======

Release Summary
---------------

Galactory finally supports the Galaxy v3 API. If you're using the upstream proxy functionality with galaxy.ansible.com then this will be required since they made the switch to galaxy_ng which no longer supports API v2.

This release contains a lot of changes, but because support for v3 is so important due to the recent galaxy change, I've tried hard to introduce no breaking changes. Deprecations scheduled for this release have been postponed to the next one.

This release should be a drop-in replacement if you're up-to-date on the previous release, without requiring any configuration changes. The v3 support and other new features and bugfixes are active in the default configuration and should have no breakages, but options are included to turn off some of those features.

Major Changes
-------------

- Galactory now supports v3 of the Galaxy API, both as a server, and as a client for upstream proxying (https://github.com/briantist/galactory/issues/23, https://github.com/briantist/galactory/pull/104).

Minor Changes
-------------

- Added the ``API_VERSION`` multi-valued config option to control which version(s) of the galaxy API are supported for the running instance (https://github.com/briantist/galactory/pull/104).
- Requests that are proxied to an upstream now override the query string to always request 100 results. This is a slight, partial mitigation for our current lack of true pagination suppport (https://github.com/briantist/galactory/issues/99, https://github.com/briantist/galactory/pull/104).
- The ``download`` endpoint now supports a query string parameter ``galactory_upstream_url``. When this parameter is set, and the requested artifact does not already exist in artifactory, this exact URL will be used as the upstream location for this file. If the parameter is not set, it uses the previous proxying behavior for downloads, which is to append the route to the configured upstream to determine its URL. API responses from galactory that contain a ``download_url`` field will automatically generate proper URLs with this parameter set, and it does not require additional configuration or user concern (https://github.com/briantist/galactory/pull/104).
- The option ``UPLOAD_FORMAT`` has been added to control the behavior of upload format detection. Auto-detection is attempted when the option is not set or set to ``auto``. Set it to ``base64`` to only accept base64-encoded content, or set it to ``raw`` to only accept unencoded content (https://github.com/briantist/galactory/pull/109).

Deprecated Features
-------------------

- The ``PREFER_CONFIGURED_KEY`` configuration option has been replaced by ``PREFER_CONFIGURED_AUTH`` and the old name will be removed in ``v0.12.0`` (https://github.com/briantist/galactory/pull/77).
- The ``PUBLISH_SKIP_CONFIGURED_KEY`` configuration option has been replaced by ``PUBLISH_SKIP_CONFIGURED_AUTH`` and the old name will be removed in ``v0.12.0`` (https://github.com/briantist/galactory/pull/77).
- The ``USE_GALAXY_KEY`` configuration option has been replaced by ``USE_GALAXY_AUTH`` and the old name will be removed in ``v0.12.0`` (https://github.com/briantist/galactory/pull/77).
- The default value of the new ``GALAXY_AUTH_TYPE`` configuration option, added in this release, will change from ``api_key`` to ``access_token`` in ``v0.12.0`` (https://github.com/briantist/galactory/pull/77).

Bugfixes
--------

- The ``href`` field of several API responses was incorrect, pointing back at the URL requested instead of pointing at the collection endpoint (https://github.com/briantist/galactory/issues/103).
- The collection publish endpoint required the file data to be base64 encoded. This worked for ``ansible-core>=2.10`` but did  not work with Ansible 2.9 or other clients that were not aware of the need. Galactory can now detect and accept both raw bytes and base64 encoded content (https://github.com/briantist/galactory/issues/105).

Known Issues
------------

- Galactory does not support proper paginated responses or proxying. This has always been the case and is not new to this release, but this bug is now tracked (https://github.com/briantist/galactory/issues/99).
- Upstream proxying does not support translation of requests between API versions. While galactory can serve both v2 and v3 simultaneously, an individual request will be proxied to the upstream as is, so the configured upstream must support the version of the request (https://github.com/briantist/galactory/pull/104).

v0.10.1
=======

Bugfixes
--------

- A collection that had only prereleases would cause a 500 error when visiting certain endpoints due to a lack of a ``latest_version`` key. The latest version is now the latest stable (non-prerelease) version if any exist, which is the same behavior as before, but if only prereleases exist, then ``latest_version`` will refer to the latest prerelease (https://github.com/briantist/galactory/issues/87).

v0.10.0
=======

Release Summary
---------------

This release adds support for Artifactory Access Tokens (bearer auth), and fixes a bug that resulted in malformed collection metadata.

Minor Changes
-------------

- Add support for Artifactory Access Tokens (bearer auth) in both configured auth and galaxy requests, via the new ``ARTIFACTORY_ACCESS_TOKEN`` and ``GALAXY_AUTH_TYPE`` configuration options (https://github.com/briantist/galactory/pull/77).
- the minimum required version of ``dohq-artifactory`` is now ``v0.9.0`` (https://github.com/briantist/galactory/pull/72).

Deprecated Features
-------------------

- The ``PREFER_CONFIGURED_KEY`` configuration option has been replaced by ``PREFER_CONFIGURED_AUTH`` and the old name will be removed in ``v0.11.0`` (https://github.com/briantist/galactory/pull/77).
- The ``PUBLISH_SKIP_CONFIGURED_KEY`` configuration option has been replaced by ``PUBLISH_SKIP_CONFIGURED_AUTH`` and the old name will be removed in ``v0.11.0`` (https://github.com/briantist/galactory/pull/77).
- The ``USE_GALAXY_KEY`` configuration option has been replaced by ``USE_GALAXY_AUTH`` and the old name will be removed in ``v0.11.0`` (https://github.com/briantist/galactory/pull/77).
- The default value of the new ``GALAXY_AUTH_TYPE`` configuration option, added in this release, will change from ``api_key`` to ``access_token`` in ``v0.11.0`` (https://github.com/briantist/galactory/pull/77).

Bugfixes
--------

- traceback when publishing or retrieving a previously published collection (even by proxying) whose metadata contains certain characters that need to be URL quoted (https://github.com/briantist/galactory/issues/58, https://github.com/briantist/galactory/issues/52).

Known Issues
------------

- any collections already published with malformed metadata due to the bug in ``collection_info`` will not be fixed and will need to be re-published or have their collection info repaired (https://github.com/briantist/galactory/pull/72).

v0.9.0
======

Release Summary
---------------

This release adds basic support for ``ProxyFix``.

Minor Changes
-------------

- factory methods - add basic support for ``ProxyFix`` to the ``create_configured_app`` factory method (https://github.com/briantist/galactory/pull/48).

v0.8.2
======

Release Summary
---------------

Another bugfix for upstream, this time for scheme rewriting in generated URLs.

Minor Changes
-------------

- site root - the root URL now has a response, just some plain text at this time but may be expanded later (https://github.com/briantist/galactory/pull/46).

Bugfixes
--------

- upstream proxying - upstream URLs are not rewritten using the preferred scheme (https://github.com/briantist/galactory/issues/45).

v0.8.1
======

Release Summary
---------------

This patch release fixes the broken upstream proxying I released previously.

Minor Changes
-------------

- containers - published container images have been updated from Python 3.10 to Python 3.11 (https://github.com/briantist/galactory/pull/41).

Bugfixes
--------

- upstream proxying - a previous change to remove deprecated JSON encoder usage in Flask inadvertently broke upstream proxying due a bad import (https://github.com/briantist/galactory/issues/42).

v0.8.0
======

Release Summary
---------------

This version is full of new features and bugfixes, and our first external contributor!

There's a new factory method that lets you re-use the same config system in place as the CLI without starting the internal web server, for use with a custom WSGI server, support for Brotli compression in upstreams, proper use of proxy environment variables, a new parameter to set a preferred URL scheme to help with reverse proxy use, and the first health check endpoint.

This release also removes use of a deprecated Flask feature (not user facing) and fixed the tests to work with Python 3.10 and 3.11, which we now test in CI.

Minor Changes
-------------

- WSGI support - in addition to the bare ``create_app`` factory function, there is now a ``create_configurd_app`` factory function, which uses the same argument parsing as running from the CLI; this allows for using an external WSGI server while taking advantage of the environment variables and configuration file support to set the configuration (https://github.com/briantist/galactory/pull/28).
- healthchecks - the first health check endpoint has been added, which can be used for load balancers, reverse proxies, smart DNS, and more (https://github.com/briantist/galactory/issues/30).
- upstream proxying - merge the ``requests`` environment for proxied requests so that environment variables such as ``REQUESTS_CA_BUNDLE`` are used appropriately (https://github.com/briantist/galactory/issues/25).

Bugfixes
--------

- generated URLs had no way to set the scheme for use reverse proxies or load balancers (https://github.com/briantist/galactory/issues/27).
- the ``/api/`` endpoint did not define a route that didn't end in ``/``, which caused Flask to issue a redirect, however the redirect does not use the preferred scheme (https://github.com/briantist/galactory/pull/29).
- the ``href`` field in responses did not use the new support for schemes (https://github.com/briantist/galactory/pull/29).
- the bare ``collections/`` endpoint was not using authorization and would have failed if authentication was required to read from Artifactory (https://github.com/briantist/galactory/pull/29).
- upstream proxying - proxied requests used the ``Accept:`` header of the request, sometimes resulting in HTML from the upstream and a resulting 500 error since the response was not JSON (https://github.com/briantist/galactory/issues/31).
- upstream proxying - proxied requests with an ``Accept-Encoding: br`` (brotli compression) header would fail decoding because of the lack of a brotli decoder (https://github.com/briantist/galactory/pull/32).

v0.7.0
======

Release Summary
---------------

Property setting is now done at upload time, which removes another piece of Pro license code, but may cause issues with certain reverse proxy configurations. A new fallback option is introduced to use the old behavior, but it may be removed in a future version.

Breaking Changes / Porting Guide
--------------------------------

- property setting - by default properties are now set on the initial upload of a collection to Artifactory. This removes an additional roundtrip to the server, and removes another API call that requires a Pro license of Artifactory. However, some reverse proxy configurations will not work with this. A new parameter ``USE_PROPERTY_FALLBACK`` has been added which will use the old behavior of setting properties in a second request,  but this will still require a Pro license to use. This option may be removed in a future version. See (https://github.com/briantist/galactory/issues/19).

v0.6.0
======

Release Summary
---------------

With this release we've added the ability to block the use of a configured API key with the publish endpoint, preventing clients from publishing anonymously.

Minor Changes
-------------

- manifest loading - galactory no longer uses Artifactory's "Archive Entry Download" endpoint, removing one piece of code that requires a pro license or greater (https://github.com/briantist/galactory/issues/5, https://github.com/briantist/galactory/pull/16).
- publish endpoint - add ``PUBLISH_SKIP_CONFIGURED_KEY`` option which disallows using a configured API key on the ``publish`` endpoint (https://github.com/briantist/galactory/issues/14).

v0.5.0
======

Release Summary
---------------

This release contains cache control options. This enables more scenarios for proxying, such as proxying with no Artifactory API key, or one without write permission. The cache expiry time can now be configured, and it can be set independently in different galactory instances pointed at the same cache in artifactory.

Minor Changes
-------------

- proxy cache - finer control over when and what gets cached when proxying upstream, allowing for proxy configurations with less permission in artifactory (https://github.com/briantist/galactory/issues/4, https://github.com/briantist/galactory/pull/13).

v0.4.0
======

Release Summary
---------------

This release adds much improved configuration support.

Minor Changes
-------------

- configuration - all options can now be configured via environment variables, direct in CLI, or in config files (https://github.com/briantist/galactory/pull/12).

v0.3.1
======

Release Summary
---------------

ARM64 containers are now part of the release process. The meaning of the ``latest`` tag for containers now refers to the build from the latest *git tag* rather than the latest commit.
Containers are now also tagged with the branch name to correspond to the latest commit in a specific branch, for example ``ghcr.io/briantist/galactory:main``.
There are no functional changes in this release.

Minor Changes
-------------

- container releases - change meaning of container tags, add ARM64 container releases (https://github.com/briantist/galactory/pull/10, https://github.com/briantist/galactory/pull/11).

v0.3.0
======

Release Summary
---------------

Some big reliability and performance enhancements included in ths release.

Minor Changes
-------------

- connections - retries are now done automatically both on proxied upstream requests and on requests to Artifactory (https://github.com/briantist/galactory/pull/7, https://github.com/briantist/galactory/pull/8).
- performance - optimizations when iterating collections allow a huge reduction in the number of requests to artifactory needed (https://github.com/briantist/galactory/pull/9).

v0.2.0
======

Release Summary
---------------

Adds a new option to control the server name in generated links.

Minor Changes
-------------

- Allow server name to be configurable via the ``--server-name`` CLI option (https://github.com/briantist/galactory/pull/3).

v0.1.0
======

Release Summary
---------------

The first release of Galactory, with support for upstream proxying.
