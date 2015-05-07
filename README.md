## In Magento:
* **Code pools**:
  * **core**
    * Holds modules that are distributed with the base Magento and make up the core functionality
  * **community**
    * Holds modules that are developed by third-parties and installed by the users
  * **local**
    * Holds custom modules developed by your company, including Mage code overrides.
  * The priority to load the files from these pools is:
    * Local
    * Comunity
    * Core
* **Module Structure:**
  ```
    Namespace/
      └── Modulename
          ├── Block
          ├── Helper
          ├── Model
          ├── controllers
          ├── etc
          └── sql
  ```
  * **Bloks**
    * Cordinate models with the template files. The files in the folder load the data from the database
      and transfer it to the templates in your theme(.phtml files).
  * **Controllers**
    * Represent all business logic actions for the given request( dispatch(), preDispatch(), postDispatch() methods) and delegate commands to other parts of the system.
  * **etc**
    * It contains all xml files that declare and configure behavior of all modules. Each module must have at least config.xml and it's a right place to declare all models, routes, blocks, helpers etc.
    * Optionally this folder could have adminhtml.xml( grant permisions for your tabs/sections in backend menus) and system.xml( creates this new section or specifies where it should be diplayed in existinh one).
  * **Helper**
    * Contain utility methods, which are commonly used in the whole system. Methods, declared in helpers, can be called from any template file or block, model, controller, class by:
      ```
        Mage::Helper('modulename/helpername')->methodName();
      ```
  * **Model**
    * In clasical MVC, Models are used to connect to the database and process the data from and to it. Magento has a different approach, which can be triky at first look.
      Magento models can be categorized in one of two ways.
        * ActiveRecord-like/one-object-one-table Model
        * Entity Attribute Value(EAV) Model.
    ***
      Each of Model also gets a **Model Collection**, Collections are PHP objects used to hold a number of invidual magento Model
      Instances. The magento team has implemented the PHP Standard Lyrbrary Interfaces of Magento Instances of IteratorAggregate and Countable
      to allow each Model type to have it's own collection type
      Each model uses two ModelResource classes:
      ```
          * one to read
          * one to write
      ```
    ***


* **Magento Templates and layout files location**
  * A theme consists of the following elements
    * **Layout:** Folder includes XML files that define the layout of the theme. Layout files work as a "glue" between the modules
    (wich are found under the app/code directory), and the template files

    ```
      app/
      ├── design
      │   ├── frontend
      │   │   ├── package_name
      │   │   │   └── theme_name
      │   │   │       ├── etc
      │   │   │       ├── <b>layout</b>
      │   │   │       └── template
    ```
  Because of magent modularity, all XML data of the default theme are stored in separate files with the name of the module to which they belong to.
A non-default theme, contrariwise, should have a sigle layout file, named local.xml, where all layout updates are placed.

* **Template**
  * This folder cointains .phtml files that have HTML and PHP code for reach Magento Blocks which will be displayed in the frontend
    ```
      app/
      ├── design
      │   ├── frontend
      │   │   ├── package_name
      │   │   │   └── theme_name
      │   │   │       ├── etc
      │   │   │       ├── layout
      │   │   │       └── <b>template</b>
    ```
* **Locale**
  * This folder contains all .CSV files organized by language that provide translations in format of languagecode_COUNTRYCODE eg(es_MX, en_US)/translate.csv
    ```
      app/
      ├── design
      │   ├── frontend
      │   │   ├── package_name
      │   │   │   └── theme_name
      │   │   │       ├── etc
      │   │   │       ├── layout
      │   │   │       └── template
      │   │   │       └── <b>locale</b>
      │   │   │           └── es_MX
      │   │   │               └── translate.csv
    ```

* **Skin and javascript files location**
  * Skin folder includes javascript, CSS and image files tha  are used in .phtml files in template folder. e.g All the files in this folder vary from theme to theme.
    ```
      skin/
      ├── frontend
      │   ├── package_name
      │   │   └── theme_name
      │   │   |   ├── <b>js</b>
      │   │   |   ├── <b>css</b>
      │   │   |   ├── <b>images</b>
    ```
  * **JS** folder embodies **js** files, libraries and frameworks that are used via frontend and backend, I f you need to add a new Javascript/AJAX library on your module from local code pool
    requires special scripts, they should be placed here.


## Design Areas:
```
  All the frontend files are stored on these areas:
```

* **adminhtml**
  * Everithing that you will observe while working in the admin panel cabe be found here. In other words
  , If a module performs anything related to admin panel, all templates and layouts will be here.
* **frontend**
  * Everything that can be observed on your webstore by any customer is stored here.
* **install**
  * Files from this area will be displayed during the installation process.
* **sql**
  * Handles any custom database tables wich will be used by the module and process all upgrades to the extension.

