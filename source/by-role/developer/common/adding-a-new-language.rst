:is-up-to-date: True
:last-updated: 5.0.0

.. meta::
   :description: Step-by-step guide to adding a new language to Crafter Studio UI including translation files, API updates, FreeMarker templates, and React locale bundles.
   :keywords: add language, Crafter Studio, localization, i18n, translation, React, FreeMarker, ISO 639, locale

.. _adding-a-new-language:

=====================
Adding a New Language
=====================
Crafter Studio has been translated into a number of languages. To reach more users, additional languages may need to be added in Crafter Studio.

**Here are the steps to add a new language in Crafter Studio:**

#. Add the new language to the ``getAvailableLanguages`` API
#. Open the file ``ui/app/src/utils/i18n.ts`` and add the locale code to ``createIntlInstance`` conditional statement, and add the switch statement case to ``fetchLocale``.
#. In Studio UI, add your new locale files to ui/app/src/translations. You may start by copying ``es.json`` and translating it into your target language.
#. Add the translation file/s for legacy forms and content type editor to ``studio-ui/static-assets/components/cstudio-common/resources/**/base.js``
#. Update templates to add the new language imports into the runtime (i.e. via script[src] elements)
#. Build, deploy and test your changes

**where:**

``**`` is the language code for the new language being added to Crafter Studio

Before we begin, we need to pick the two letter language code for the new language we are adding for Crafter Studio.  You can use `ISO 639 language codes <https://www.iso.org/iso-639-language-codes.html>`_ for reference.  So, say, we are adding Japanese, from the ISO 639 language codes, we will be using ``ja``

Let's begin adding the language ``Japanese`` to Crafter Studio:

----------------------------------------------------------
1. Add the new language to the available_languages API
----------------------------------------------------------
* To add the new language to the :base_url:`getAvailableLanguages <_static/api/studio.html#tag/system/operation/getAvailableLanguages>`, in your Studio configuration override file, ``studio-config-override.yaml``, add the new language:

  .. code-block:: yaml
     :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
     :emphasize-lines: 10-11
     :linenos:

     studio.configuration.availableLanguages:
         - id: en
           label: English
         - id: es
           label: Español
         - id: ko
           label: 한국어
         - id: de
           label: Deutsch
         - id: ja
           label: 日本語


---------------------------------------------------------
2. Add the New Language to the React Translations Manager
---------------------------------------------------------
In your ``studio-ui`` code, open the file ``ui/app/src/utils/i18n.ts`` and add the locale code to the ``createIntlInstance`` conditional statement, and add the new language ``ja`` to the ``ImportsLookup``.

.. code-block:: ts
    :caption: *studio-ui/ui/app/src/utils/i18n.ts*
    :emphasize-lines: 1,5,10,15

    const importsLookup: ImportsLookup = {
	    de: () => import('../translations/de.json'),
	    es: () => import('../translations/es.json'),
	    ko: () => import('../translations/ko.json'),
	    ja: () => import('../translations/ja.json')
    };

    ...

    async function createIntlInstance(localeCode: string): Promise<IntlShape> {
      localeCode = localeCode.replace('kr', 'ko');
      if (
        !fetchedLocales[localeCode] &&
        // Nothing to fetch point if we don't have the locale
        ['de', 'es', 'kr', 'ja'].includes(localeCode)
      ) {
        let fetchedTranslations = await fetchLocale(localeCode as BundledLocaleCodes);
        ...

------------------------------------------------------------------
3. Add Your New Locale File/s to studio-ui/ui/app/src/translations
------------------------------------------------------------------
In your ``studio-ui`` code, add the new locale files to ``ui/app/src/translations``. You may start by copying ``es.json`` and translating it into your target language.

.. code-block:: json
    :force:
    :caption: *studio-ui/ui/app/src/translations/ja.json*

    {
        "+E4CL4": "プロジェクト全体が公開されました",
        "+fYahR": "サポートタイプ",
        "+vOP15": "コンテンツの名称変更",
        ...
        "about.versionNumber": "バージョン番号",
        "aboutView.attribution": "CrafterCMS 他の人のおかげでそれは可能です <a>オープンソースソフトウェアプロジェクト</a>.",
        "accountManagement.changeHelperText": "パスワードが正常に更新されると、再度ログインするように求められます.",
        "accountManagement.changeLanguage": "言語の変更",
        "accountManagement.changePassword": "パスワードを変更する",
        ...
    }

--------------------------------------------------------------------
4. Update Templates to Add the New Language Imports Into the Runtime
--------------------------------------------------------------------
* We now need to update templates to add the new language imports into the runtime (i.e. via script[src] elements).  In your ``studio-ui`` code, navigate to ``studio-ui/templates/web/``.  The following templates need to be updated:

  * form.ftl
  * legacy-site-config.ftl

* Add the new language imports ``<script src="/studio/static-assets/components/cstudio-common/resources/ja/base.js"></script>`` into the files listed above:

  .. code-block:: html
      :force:
      :linenos:
      :emphasize-lines: 8
      :caption: *studio-ui/templates/web/form.ftl*

      <#include "/templates/web/common/page-fragments/head.ftl" />
      <#include "/templates/web/common/page-fragments/studio-context.ftl" />

      <script src="/studio/static-assets/components/cstudio-common/resources/en/base.js"></script>
      <script src="/studio/static-assets/components/cstudio-common/resources/kr/base.js"></script>
      <script src="/studio/static-assets/components/cstudio-common/resources/es/base.js"></script>
      <script src="/studio/static-assets/components/cstudio-common/resources/de/base.js"></script>
      <script src="/studio/static-assets/components/cstudio-common/resources/ja/base.js"></script>

  .. code-block:: html
      :force:
      :linenos:
      :emphasize-lines: 5
      :caption: *studio-ui/templates/web/legacy-site-config.ftl*

      <script type="text/javascript" src="/studio/static-assets/components/cstudio-common/resources/en/base.js"></script>
      <script type="text/javascript" src="/studio/static-assets/components/cstudio-common/resources/kr/base.js"></script>
      <script type="text/javascript" src="/studio/static-assets/components/cstudio-common/resources/es/base.js"></script>
      <script type="text/javascript" src="/studio/static-assets/components/cstudio-common/resources/de/base.js"></script>
      <script type="text/javascript" src="/studio/static-assets/components/cstudio-common/resources/ja/base.js"></script>


---------------------------------------------------------------
5. Add the Translations File for Legacy Forms and Content Types
---------------------------------------------------------------
* To add the translations file for legacy forms and content types, in your ``studio-ui`` code, navigate to ``studio-ui/static-assets/components/cstudio-common/resources/``.  Create a folder using the two letter language code for the new language being added, ``studio-ui/static-assets/components/cstudio-common/resources/ja``
* Copy the file ``studio-ui/static-assets/components/cstudio-common/resources/en/base.js`` and paste it into the newly created folder
* Start translating the content in ``studio-ui/static-assets/components/cstudio-common/resources/ja/base.js`` and save your changes

    .. code-block:: js
       :caption: *studio-ui/static-assets/components/cstudio-common/resources/ja/base.js*
       :linenos:

       CStudioAuthoring.Messages.registerBundle('siteDashboard', 'ja', {
       dashboardTitle: 'ダッシュボード',

       dashboardCollapseAll: 'すべて折りたたむ',
       ...

  Remember to change the language code in all the ``registerBundle`` calls in the ``base.js`` file

  .. code-block:: js

      CStudioAuthoring.Messages.registerBundle('siteDashboard', 'ja', {
      ...
      CStudioAuthoring.Messages.registerBundle('contextnav', 'ja', {
      ...

--------------------------------------
6. Build, deploy and test your changes
--------------------------------------
Don't forget to build and deploy.  Before building, remember to run prettier on the file ``ui/app/src/utils/i18n.ts``

.. code-block:: bash
    :caption: *src/studio-ui*

    cd ui/app
    yarn prettier --config ../../prettier.config.js --write ./src/utils/i18n.ts

After running prettier, build and deploy your changes, then start it:

.. code-block:: bash

    ./gradlew build deploy start

To test your changes, from the login screen, click on the language dropdown box, and you should see the new language we just added.

.. image:: /_static/images/system-admin/login-new-lang.webp
   :align: center
   :width: 65 %
   :alt: Japanese Language Added
