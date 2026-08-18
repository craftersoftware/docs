:is-up-to-date: True
:last-updated: 4.1.2

.. meta::
   :description: Step-by-step guide to adding a new language to Crafter Studio UI including translation files, API updates, FreeMarker templates, and React locale bundles.
   :keywords: add language, Crafter Studio, localization, i18n, translation, React, FreeMarker, ISO 639, locale

.. _adding-a-new-language:

=====================
Adding a New Language
=====================
Crafter Studio has been translated into a number of languages. To reach more users, additional languages may need to be added in Crafter Studio.

**Here are the steps to add a new language in Crafter Studio:**

#. Add the new language to the ``get-available-languages`` API
#. In Studio UI, add your new locale files to ui/app/src/translations. You may start by copying ``es.json`` and translating it into your target language.
#. Open the file ``ui/app/src/utils/i18n.ts`` and add the locale code to ``createIntlInstance`` conditional statement, and add the switch statement case to ``fetchLocale``.
#. Add the translation file/s for legacy forms and content type editor to ``studio-ui/static-assets/components/cstudio-common/resources/**/base.js``
#. Update templates to add the new language imports into the runtime (i.e. via script[src] elements)
#. Build, deploy and test your changes

**where:**

``**`` is the language code for the new language being added to Crafter Studio

Before we begin, we need to pick the two letter language code for the new language we are adding for Crafter Studio.  You can use `ISO 639 language codes <https://www.iso.org/iso-639-language-codes.html>`_ for reference.  So, say, we are adding Japanese, from the ISO 639 language codes, we will be using ``ja``

Let's begin adding the language ``Japanese`` to Crafter Studio:

----------------------------------------------------------
1. Add the new language to the get-available-languages API
----------------------------------------------------------
* To add the new language to the ``get-available-languages`` API (*/studio/api/1/services/api/1/server/get-available-languages.json*), in your ``studio`` code, navigate to ``studio/src/main/webapp/default-site/scripts/rest/api/1/server`` and open the ``get-available-languages.get.groovy`` file
* Add the new language to the file:

  .. code-block:: groovy
     :emphasize-lines: 14-16
     :linenos:

     def result = []
    	result[0] = [:]
    	result[0].id = "en"
    	result[0].label = "English"
    	result[1] = [:]
    	result[1].id = "es"
    	result[1].label = "español"
    	result[2] = [:]
    	result[2].id = "ko"
    	result[2].label = "한국어"
    	result[3] = [:]
    	result[3].id = "de"
    	result[3].label = "Deutsch"
        result[4] = [:]
        result[4].id = "ja"
        result[4].label = "日本語"
     return result

---------------------------------------------------------
2. Add the New Language to the React Translations Manager
---------------------------------------------------------
Open the file ``ui/app/src/utils/i18n.ts`` and add the locale code to the ``createIntlInstance`` conditional statement, and add the switch statement case to ``fetchLocale``.

.. code-block:: ts
    :emphasize-lines: 1,13-15,23,28

    async function fetchLocale(locale: string): Promise<LookupTable<string>> {
      let translations;
      switch (locale) {
        case 'de':
          translations = await import('../translations/de.json');
          break;
        case 'es':
          translations = await import('../translations/es.json');
          break;
        case 'ko':
          translations = await import('../translations/ko.json');
          break;
        case 'ja':
          translations = await import('../translations/ja.json');
          break;
        default:
          translations = Promise.resolve({});
          break;
      }
      return translations;
    }

    async function createIntlInstance(localeCode: string): Promise<IntlShape> {
      localeCode = localeCode.replace('kr', 'ko');
      if (
        !fetchedLocales[localeCode] &&
        // Nothing to fetch point if we don't have the locale
        ['de', 'es', 'kr', 'ja'].includes(localeCode)
      ) {
        let fetchedTranslations = await fetchLocale(localeCode as BundledLocaleCodes);
        ...

--------------------------------------------------------
3. Add Your New Locale File/s to ui/app/src/translations
--------------------------------------------------------
In your ``studio-ui`` code, add the new locale files to ``ui/app/src/translations``. You may start by copying ``es.json`` and translating it into your target language.

.. code-block:: json
    :force:
    :caption: *ui/app/src/translations/ja.json*

    {
      "+E4CL4": "プロジェクト全体が公開されました",
      "/A7dEh": "最後の投稿はエラーで完了しました、詳細についてはログを参照してください。",
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

       CStudioAuthoring.Messages.registerBundle("siteDashboard", "ja", {
       dashboardTitle: "ダッシュボード",

       dashboardCollapseAll: "すべて折りたたむ",
       ...

Remember to change the language code in the all the ``registerBundle`` calls in the ``base.js`` file

  .. code-block:: js

     CStudioAuthoring.Messages.registerBundle("dialogs", "ja", {


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
