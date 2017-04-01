.. _edx_user_interfaces:

##########################
Developing User Interfaces
##########################

This section provides information about developing user interfaces (UI) for edX
applications.

Open edX is a large platform that implements its user interfaces in a variety of
ways. The major applications (Studio, LMS, Insights, Otto) are built as Django
views that return rendered pages consisting of HTML, CSS and JavaScript. In
addition, components can be created with `XBlocks`_ which use Python code to
render themselves as HTML fragments along with their CSS and JavaScript
dependencies.

.. Add an overview of the current architecture.
.. * Django views generated using Mako templates
.. * Dynamic pages created using RequireJS, Backbone and Underscore,
..   making AJAX calls to Django REST APIs
.. * Sass is used to create style sheets and libsass is used to convert
..   them to CSS files
.. * Production pages are bundled and minified using RequireJS
..   Optimizer and served through the Django Pipeline

.. Add a performance section that explains best practices for ensuring
.. performant pages.

The Open edX wiki includes additional details about developing edX user
interfaces. For more information, see `edX Front End Development`_.

.. contents::
 :local:
 :depth: 2


.. 20160518 - any important information about UI development should be moved
.. from the wiki to the developer's guide here.


**************
Best Practices
**************

There are a number of best practices to consider before starting to develop
a new user interface.

1. **Use server-side rendering whenever possible**

   Rationale: Server-side rendering gives the content to the user
   immediately without requiring additional round-trips. This can make
   for large performance improvements on slow networks or mobile devices.

   When a dynamic client-side experience is required, then the server-side
   page should include JavaScript code along with the HTML. See the
   technical recommendations below for more detail.

2. **Front-ends should be self-contained**

   Rationale: Front end applications require a variety of types of assets
   that need to be managed together, including code, styles, templates, and
   images. For Django apps and XBlocks, all of these assets should live
   within the app's or block's own directory rather than being located
   in shared locations within the consuming application. Ideally, apps or blocks
   should live in their own repository and should be pip installed into
   the main application.

3. **Designs should be built upon patterns from the edX Pattern Library**

   Rationale: The edX Pattern Library is a collection of web application
   components and visual styles that can be used to develop UIs that are
   consistent with the edX platform. Using the pattern library ensures that
   web applications follow best practices for user experience, theming,
   and accessibility.

   You can learn more in the section `Using the edX Pattern Library`_.

4. **Front-ends should consume and extract reusable UI components**

   Rationale: when developing new applications, developers should prefer
   reuse of existing components over building custom front end logic.
   If a new component is required, it should be built as a new reusable
   addition to the edX UI Toolkit`, based upon patterns from the
   edX Pattern Library. This ensures that future development can
   reuse this component, while also standardizing how it behaves
   for the user in terms of user interface, accessibility, theming
   and so on.

   You can learn more in the section `Using the edX UI Toolkit`_.

6. **Features should work equally well on mobile and web platforms**

   Rationale: Open edX provides iOS and Android applications in addition
   to its web applications, and so new learner features need to be implemented
   for all three platforms. The simplest way to achieve feature parity is to
   provide a responsive web experience that can be used on phones and tablets
   as well as on desktops and laptops. The edx Pattern Library helps with
   this by providing a responsive grid system along with components that
   work equally well on mobile and web.

7. **Designs should be fully accessible**

   Rationale: Open edX measures and evaluates accessibility using the World
   Wide Web Consortium’s `Web Content Accessibility Guidelines (WCAG) 2.0`_.
   All features merged into edX repositories are expected to conform to
   Level AA of this specification and to satisfy the requirements outlined in
   the `edX Website Accessibility Policy`_.

   You can learn more about developing accessible applications in
   the chapter :doc:`../conventions/accessibility`.

8. **Front end applications should support internationalization**

   Rationale: Open edX is used by learners all over the world, and so
   it is critical that all new functionality can be localized. This means
   that every string in an application should be marked for translation,
   and also includes ensuring that all new user interfaces will switch to
   right-to-left mode when the language requires it.

   You can learn more about internationalization in the section
   :doc:`../conventions/internationalization/index`.

9. **Front end implementations should support theming**

   Rationale: Open edX is used by organizations around the world,
   and each one requires that the user experience is based upon their
   own brand. This includes elements as simple as overriding the fonts,
   imagery and color schemes, to more complex customization of headers,
   footers and registration flows.

   One simple way to make your interface support theming is to use the patterns
   from the edX Pattern Library. You can learn more about how Open edX supports
   theming with the guide `Changing Themes for an Open edX Site`_.

10. **Front end code should be safe from cross site scripting**

   Cross Site Scripting (XSS) vulnerabilities allow user-supplied data to be
   incorrectly executed as code in a web browser. This can allow an attacker
   to inject malicious scripts, which in the worst case can cause the loss
   of private user data.

   In order to keep your code safe from such attackers, you should follow the
   best practices laid out in the chapter :doc:`../conventions/safe_templates`.

********************
Technology Standards
********************

The following is the current recommendation for technologies to use when
developing new user interfaces. It is important to always use the best tool for
the task at hand, so there will be situations where an alternative approach or
technology should be adopted.

For each of these recommendations, see `OEP-11: Front End Technology Standards`_
for more detail along with justifications, and :ref:`edx_javascript_guidelines`
for how to write the code.

1. **Server-side content should be rendered with Django Templates**

   **Rationale**: There are many template languages available for Django,
   but the simplest option is to use the built-in Django template engine.
   The Open edX codebase has a mixture of Django and Mako templates, but the
   former are easier to reason about because they don't support arbitrary
   code evaluation.

   **Exception**: Mako templates can continue to be used within edx-platform
   for consistency with the existing code base. This is because the base
   templates and theming support are all provided via Mako, so it is too
   much to expect a new feature to be implemented with a different framework.
   There is much desire to replace Mako within edx-platform so this
   exception may eventually be removed.

2. **Target the latest standardised JavaScript version (ECMA-262)**

   **Rationale**: edX JavaScript should be written consistent with the latest
   ECMA-262 specification in order to ensure future support, the largest
   community and the availability of modern features. To support this syntax
   in older browsers, use `Babel`_. Babel may also be configured to add syntax
   extensions widely adopted by the community of our recommended framework
   (e.g., `JSX`_).

   **Exception**: Much of edX's existing front end code is written conformant
   to the version of ECMA-262 released in 2009 (ES5). Files written in ES5
   should be gradually converted to the newer standard as new development in
   those feature areas requires.

   **Note**: edX previously used CoffeeScript, but its use has now been
   deprecated. Community interest in TypeScript has also grown, but it and
   other languages that do not follow the ECMA-262 spec should not be used.

3. **JavaScript libraries should be installed via npm**

   **Rationale**: It is important that JavaScript libraries are kept
   up-to-date, and one key component is to make it as simple as possible
   to perform upgrades. Projects that use npm maintain all of their
   library requirements in a single `package.json` file, which can be
   easily changed as the versions change.

4. **JavaScript code should follow the edX ESLint configuration**

   **Rationale**: In order to standardize and enforce Open edX's JavaScript
   coding style across multiple codebases, edX has published an ESLint
   configuration that provides an enforceable specification. The
   `edX ESLint Config`_ is made available as an npm package that can be
   installed into any Open edX package.

   **Exception**: The `edX ESLint Config for ES5`_ may be used where ES5
   is in use. Both configs may be used in the same codebase through the
   use of `ESLint configuration cascading`_.

5. **Use React and Redux when building new client-side applications**

   **Rationale**: React should be used for building new UIs, as it is
   widely adopted by the community and strikes a balance between
   flexibility and feature richness. For state management of complex
   client-side interactions, Redux should be used. This library was chosen
   because it sees strong use in the React community, but is also flexible
   enough to be used in situations where a hybrid React/Backbone architecture
   exists.

   See `React at edX`_ for more details on the evaluation process that led to
   this technology selection. Other modern frameworks considered for use
   included Polymer and Angular. See `Reasons for rejecting Angular`_  and
   `Reasons for rejecting Polymer`_ for an exploration of why these
   alternatives do not work as well as React for edX.

   **Exception**: When building new applications within edx-platform, it
   is currently acceptable to use Backbone.  Backbone is a somewhat old
   technology in the JavaScript world and has seen a rapid drop off in usage
   within the community. The plan is to incrementally adopt React within
   edx-platform as it can interoperate cleanly with Backbone. More
   documentation on this plan can be found in
   `Modernizing the edX front end stack`_.

6. **JavaScript should be bundled using Webpack**

   **Rationale**: `Webpack`_ is the tool of choice for code optimization and
   bundling, as it is widely used throughout the industry, and because
   it seamlessly handles modern code bases as well as all of the older
   technologies used by edX, such as `AMD Modules`_. Webpack should be
   implemented to handle as much of the "asset pipeline" as possible,
   rather than passing this responsibility on to Django.

   **Exception**: Existing code bases that use `RequireJS`_ and
   `RequireJS Optimizer`_ can continue to do so, but opportunities should
   be sought to switch over to Webpack.

7. **JavaScript dependencies should be managed with ES2015 Modules**

   **Rationale**: JavaScript module systems allow front end code to specify
   its dependencies and be grouped into bundles that minimize the assets
   needed to provide page functionality. The most prevalent module syntax
   is currently `ES2015 Modules`_, which should be adopted everywhere
   edX code is written to the ES2015 spec or later.

   **Exception**: Much of edX's existing (ES5) JavaScript uses the older
   `AMD Modules`_ syntax for modules. AMD Modules are interoperable
   with ES2015 Modules if Webpack is used for bundling, so AMD is an
   acceptable module definition if the code must remain ES5.

8. **CSS should be generated using Sass**

   **Rationale**: Sass's SCSS syntax is an extension of CSS that adds power
   and elegance to the basic language. It makes the maintenance of large
   CSS files more manageable though the ability to use variables, mixins,
   imports and more. In particular, it makes theming possible with the
   ability to override variables that define colors, fonts etc.

   You can find out more about Sass in the official `Sass documentation`_.

.. _Using the edX Pattern Library:

*****************************
Using the edX Pattern Library
*****************************

The edX Pattern Library is a collection of web application
components and visual styles that you can use to develop UIs that
are consistent with the edX platform.

The edX Pattern Library is available at `ux.edx.org`_.

The edx-platform GitHub repository includes an `example web page`_ that
demonstrates using the Pattern Library. You can use the example page as a
template for creating your own new LMS or Studio pages.

To create a new page using the Pattern Library, you use the same ``main.html``
template as for non-Pattern Library pages, and pass the following context
parameters.

    .. code-block:: none

        {
            ...
            'disable_courseware_js': True,
            'uses_pattern_library': True,
        }

When you create a page using the Pattern Library, create two new root
syntactically awesome style sheets (Sass) files for your page, one for left-to-
right (LTR) and one for right-to-left (RTL) localizations. For more information
about Sass, see the `Sass documentation`_.

The RTL version of the style sheet must have the same filename as the
LTR version but ending with ``-rtl``.

Register the two new style sheets in the ``PIPELINE_CSS`` configuration. You
can see the full list for the LMS in `lms/envs/common.py`_.

After you register the LTR and RTL style sheets, you refer to the configuration
name in your Mako template. Mako will then dynamically pick either the LTR or
the RTL version based upon the current user's localization preference.

The following example shows how a pair of style sheets are registered in the
``PIPELINE_CSS`` configuration.

.. code-block:: python

    'style-learner-dashboard': {
        'source_filenames': [
            'css/lms-learner-dashboard.css',
        ],
        'output_filename': 'css/lms-learner-dashboard.css',
    },
    'style-learner-dashboard-rtl': {
        'source_filenames': [
            'css/lms-learner-dashboard-rtl.css',
        ],
        'output_filename': 'css/lms-learner-dashboard-rtl.css',
    },

After you register your Sass style sheets, you must also declare the name of
your Sass files at the top of your main Mako file. The following example
declares files that are configured with the names ``style- learner-dashboard``
and ``style-learner-dashboard-rtl``.

.. code-block:: mako

    <%! main_css = "style-learner-dashboard" %>

You can refer to the following examples.

* `Pattern Library test page`_

* PR to convert the programs list to be a Pattern Library page: `PR 12380`_


.. _ui_toolkit:

************************
Using the edX UI Toolkit
************************

The edX UI Toolkit is a JavaScript library for building edX web applications.
The UI toolkit consists of:

* Backbone views to implement patterns as defined by the edX Pattern Library.

* Utility methods to simplify the creation and testing of user interfaces.

The edX UI Toolkit is available from the `edx-ui-toolkit GitHub repository`_ .
For more information about the UI Toolkit, see `ui-toolkit.edx.org`_ .

.. _adding_a_new_ui_page:

****************
Adding a UI Page
****************

This topic is a collection of things to think about when creating a new page
in Studio or the LMS.

You should also read :ref:`Contributing to Open edX` to understand
more general best practices, as well as how to submit your new code for review.

.. contents::
 :local:
 :depth: 2

===========================
Adding JavaScript Libraries
===========================

Install JavaScript libraries using the ``npm`` package manager.

If the library you want to add is available from `npmjs.com`_,  update edx-
platform's package.json file to reference the library. Use the tilde ``~``
prefix for the version to allow patches to be picked up automatically.

For more information about versioning, see `semver.org`_.

Execute the following command to install your library.

.. code-block:: shell

    paver install_prereqs

Add the new library to the `list of npm-installed libraries`_.

Execute the following command to make your library available as a Django static
asset (choose LMS, Studio, or both).

.. code-block:: shell

    paver update_assets lms --settings=devstack

To add the new library to the LMS, follow these steps.

#. Add the library to the `lms/env/common.py` file at https://github.com/edx
   /edx-platform/blob/master/lms/envs/common.py#L1246

#. Add the library to `lms/static/lms/js/require-config.js`_.

Add the new library to the list of vendor libraries that are installed by
``update_assets``. Update the variable ``NPM_INSTALLED_LIBRARIES`` in
`/pavelib/assets.py`_.

.. note::

    Reference the unminified version of the library. This allows debugging
    tools to step through readable library code, and the Django static asset
    pipeline will ensure that it get optimized for production.

If you cannot use npm, check the file into the `common/static/js/vendor`_
directory.

.. _use_underscore_and_requirejs_text:

===========================
Working with LMS and Studio
===========================

The code base for LMS and Studio does not follow all of these best practices
as it has been developed over a number of years. There is material on the
Open edX wiki describing how to work in that code. See
`How to add a new feature to LMS or Studio`_ for more details.

.. Link destinations

.. _/pavelib/assets.py: https://github.com/edx/edx-platform/blob/master/pavelib/assets.py#L47
.. _AMD Modules: https://github.com/amdjs/amdjs-api/wiki/AMD
.. _Angular: https://angular.io/
.. _Babel: https://babeljs.io/
.. _Changing Themes for an Open edX Site: http://edx.readthedocs.io/projects/edx-installing-configuring-and-running/en/open-release-eucalyptus.master/configuration/changing_appearance/theming/index.html
.. _common/static/js/vendor: https://github.com/edx/edx-platform/tree/master/common/static/js/vendor
.. _Courseware Search (LMS): https://github.com/edx/edx-platform/blob/master/lms/static/js/search/course/views/search_results_view.js
.. _edX ESLint Config for ES5: https://github.com/edx/eslint-config-edx/tree/master/packages/eslint-config-edx-es5
.. _edX ESLint Config: https://github.com/edx/eslint-config-edx/tree/master/packages/eslint-config-edx
.. _edX Front End Development: https://openedx.atlassian.net/wiki/display/FEDX/edX+Front+End+Development
.. _edX UI Toolkit: http://ui-toolkit.edx.org/
.. _edX Website Accessibility Policy: https://www.edx.org/accessibility
.. _ES2015 Modules: http://www.ecma-international.org/ecma-262/6.0/#sec-imports
.. _ESLint configuration cascading: http://eslint.org/docs/user-guide/configuring#configuration-cascading-and-hierarchy
.. _How to add a new feature to LMS or Studio: https://openedx.atlassian.net/wiki/display/AC/How+to+add+a+new+feature+to+LMS+or+Studio
.. _JSX: https://facebook.github.io/react/docs/introducing-jsx.html
.. _Modernizing the edX front end stack: https://openedx.atlassian.net/wiki/display/FEDX/Modernizing+the+edX+front+end+stack
.. _OEP-11: Front End Technology Standards: http://open-edx-proposals.readthedocs.io/en/latest/oep-0011.html
.. _PR 12380: https://github.com/edx/edx-platform/pull/12380
.. _Pattern Library test page: https://github.com/edx/edx-platform/blob/master/lms/templates/ux/reference/pattern-library-test.html
.. _Polymer: https://www.polymer-project.org/
.. _React at edX: https://openedx.atlassian.net/wiki/display/FEDX/React+at+edX
.. _React: https://github.com/facebook/react
.. _RequireJS Optimizer: http://requirejs.org/docs/optimization.html
.. _RequireJS: http://requirejs.org/
.. _Sass documentation: http://sass-lang.com/
.. _Typescript: https://www.typescriptlang.org/
.. _Web Content Accessibility Guidelines (WCAG) 2.0: http://www.w3.org/TR/WCAG/
.. _Webpack: https://webpack.github.io/
.. _example web page: https://github.com/edx/edx-platform/blob/master/lms/templates/ux/reference/pattern-library-test.html
.. _list of npm-installed libraries: https://github.com/edx/edx-platform/blob/master/pavelib/assets.py#L43
.. _lms/envs/common.py: https://github.com/edx/edx-platform/blob/master/lms/envs/common.py#L1373
.. _lms/static/lms/js/require-config.js: https://github.com/edx/edx-platform/blob/master/lms/static/lms/js/require-config.js#L51>
.. _npmjs.com: https://www.npmjs.com/
.. _semver.org: http://semver.org/

.. _

.. include:: ../../links/links.rst
