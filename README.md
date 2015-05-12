* [Observer Pattern](#observer)
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


* **Class naming conventions and their relationship with the autoloader**
  >Magento was developed based on the Zend Framework, so the rules of class naming in Magento were taken from the Zend Framework

  Magento standardizes class names depending on their location in the file system. Such standadization enables atomatic class loading(**autoloader**)
  instead of using require_once and include_once functions. Rather than the directory separator('/' - invalid character for class names), developers use the underscor
  character('_')

  For example, the Mage_Catalog_Model_Product class is located in the /app/code/core/Mage/Catalog/Model/Product.php category. Magento Autoloader replaces all underscore characters
('_') with the category separator('/') and looks for the file in one of the categories(/app/code/local/ can be disables in the app/etc/local.xml in the disable_local_modules tag).

  - /app/code/core
  - /app/code/community
  - /app/code/local
  - /lib/
> File search categories in Magento are defined in app/Mage.php
```php
  Mage::register('original_include_path', get_include_path());

  if (defined('COMPILER_INCLUDE_PATH')) {
      $appPath = COMPILER_INCLUDE_PATH;
      set_include_path($appPath . PS . Mage::registry('original_include_path'));
      include_once "Mage_Core_functions.php";
      include_once "Varien_Autoload.php";
  } else {
      /**
       * Set include path
       */
      $paths[] = BP . DS . 'app' . DS . 'code' . DS . 'local';
      $paths[] = BP . DS . 'app' . DS . 'code' . DS . 'community';
      $paths[] = BP . DS . 'app' . DS . 'code' . DS . 'core';
      $paths[] = BP . DS . 'lib';

      $appPath = implode(PS, $paths);
      set_include_path($appPath . PS . Mage::registry('original_include_path'));
      include_once "Mage/Core/functions.php";
      include_once "Varien/Autoload.php";
  }

  Varien_Autoload::register();
  ```
  After defining all include paths you see, at the very last line of above snippet, that
  Magento's autoloader ins initialized. This line triggers the underneath snippet, which registers
  the autoload() method of the Varient_Autoloader object as it's autoloader weapon of choice.
  So behind the scenes Magento will now run each classname through this method to get to it's related file.

* **Describe methods for resolving module conflicts.**
  * There are **3 levels** of module compatibility conflicts
    * Conflicts with configuration files
      * When thow modules use the same class dependence, future conflicts may appear in the program parts of modules(improver use of the class constructor). This group of problems
        is closel connected to our second group 'conflicts with software part'.
        eg:

        Namespace_OtherModulename.xml

        ```xml
          <config>
              <modules>
                  <Namespace_OtherModulename>
                      <active>true</active>
                      <codePool>community</codePool>
              <depends><Mage_Something/></depends>
                  </Namespace_OtherModulename>
              </modules>
          </config>
        ```
        Namespace_Modulename.xml

        ```xml
          <config>
              <modules>
                  <Namespace_Modulename>
                      <active>true</active>
                      <codePool>community</codePool>
              <depends><Mage_Something/></depends>
                  </Namespace_Modulename>
              </modules>
          </config>
        ```

        We can fix them by replacing with the next code:

        Namespace_OtherModulename.xml

          ```xml
            <config>
                <modules>
                    <Namespace_OtherModulename>
                        <active>true</active>
                        <codePool>community</codePool>
                <depends><Mage_Something/></depends>
                    </Namespace_OtherModulename>
                </modules>
            </config>
          ```
          Namespace_Modulename.xml

          ```xml
              <config>
                  <modules>
                      <Namespace_Modulename>
                          <active>true</active>
                          <codePool>community</codePool>
                  <depends><Namespace_OtherModulename/></depends>
                      </Namespace_Modulename>
                  </modules>
              </config>
          ```

    * Conflicts with the software part
      * The main reason for module conflicts could be in the use of dependencies and class rewriting. You should regard your module
        configuration file(/Namespace_Modulename/etc/config.xml), in which class rewriting with `<rewrite></rewrite>` tags can be used. Eg

        ```xml
            <customer>
              <rewrite>
                   <form_edit>Namespace_Modulename_Block_Rewrite_BlockClass</form_edit>
               </rewrite>
            </customer>
        ```
        Incorrect use of class methods is the main reason of conflicts origin. There are several ways to solve this problem, but I would like to highlight the very best
        and most correct way in my opinion
        - The elimination of the classes overriding. The idea of this method is similar to the previous method of module conflict resilving and offers setting
          Dependence of one class from another.

          `Namespace_Modulename/etc/config.xml`

          ```xml
            <customer>
              <rewrite>
                <form_edit>Namespace_Modulename_Block_Rewrite_BlockClass</form_edit>
              </rewrite>
            </customer>
          ```
          `Namespace_OtherModulename/etc/config.xml`

          ```xml
            <customer>
              <rewrite>
                <form_edit>Namespace_OtherModulename_Block_Rewrite_BlockClass</form_edit>
              </rewrite>
            </customer>
          ```
        - Solution:
        First remove the rewriting for the first module

        ```xml
          <customer>
            <form_edit> Namespace_Modulename_Block_Rewrite_BlockClass</form_edit>
          </customer>
        ```
        Second Use the first class inheritance from the second one:

        ```
          Class Namespace_Modulename_Block_Rewrite_BlockClass extends Namespace_OtherModulename_Block_Rewrite_BlockClass
        ```

    * Conflicts in a module display
      * The most frquent conflict appears while using several modules, problems with displaying module on the frontend.
      * Some reasons:
        * Blocks overrding in layout settings
        * Use different template for the same blocks
        * Removing blocks for another module to be embedded in.

* **Explain how Magento loads and manipulates configuration information**
  At very basic level, when you load up your store, the following happens:

  1. The basic configuration is initialized
  2. Module configuration is initialized

  * Each module on Magento must include a `config.xml` file located on
  `app/code/[codePool]/Namespace/Modulename/etc/config.xml`
  This file contains all basic module setting and `app/etc/modules/Namespace_Modulename.xml` contains
  information about code pool and extension activation flag.

  * Global settings for Magento instalation, inlcuding database connection data and admin panel addrress, are
  sorted in `app/etc/config.xml` and `app/etc/local.xml`.

  > Everything starts on the **index.php** file with the next lines:
  ```php
    /* Store or website code */
    $mageRunCode = isset($_SERVER['MAGE_RUN_CODE']) ? $_SERVER['MAGE_RUN_CODE'] : '';

    /* Run store or run website */
    $mageRunType = isset($_SERVER['MAGE_RUN_TYPE']) ? $_SERVER['MAGE_RUN_TYPE'] : 'store';

    Mage::run($mageRunCode, $mageRunType);
  ```
  If we trace the code starting from the inex.php we get the following outcome:

  ```php
  Index.php
    Mage::run()
      self::$_app = new Mage_Core_Model_App();
      self::$_app = run(...);
  ```
  The `Mage::run()` all wrappers for precessing configuration loading are located in `Mage_Core_Model_App` and refer
  to the `Mage_Core_Model_Config` methods. Calling `the Mage::app()`, we immediately call `Mage_Core_Model_Config::init()`, which contains
  the process of configuration loadig.

  Finally we come to `Magento_Core_Model_Config`, inherited from `Mage_Core_Model_Config_Base` and `Varien_Simplexml_Config`.

  So let's refer to:  `Mage_Core_Model_Config::init()` method:
  ```php
    public function init($options=array())
      {
          $this->setCacheChecksum(null);
          $this->_cacheLoadedSections = array();
          $this->setOptions($options);
          $this->loadBase();

          $cacheLoad = $this->loadModulesCache();
          if ($cacheLoad) {
              return $this;
          }
          $this->loadModules();
          $this->loadDb();
          $this->saveCache();
          return $this;
      }
  ```
  ``$this->loadBase();``

    From the beginning we define the absolute path to `app/etc` directory. Then we get a list of all
  .xml fules from this catalog, read their content and merge into a **Single simpleXmlElement Object**

  If local.xml file has been loaded( which generally means that Magento as already been installed). set flag
  `$this->_isLocalConfigLoaded = true;` It will be used later to initialize store and load module setup scripts.

  The next spe is loading of the most extensive configuration part - `Modules configuration.`

  **$this->loadModules();**

  ```php
    /**
         * Load modules configuration
         *
         * @return Mage_Core_Model_Config
         */
        public function loadModules()
        {
            Varien_Profiler::start('config/load-modules');
            $this->_loadDeclaredModules();

            $resourceConfig = sprintf('config.%s.xml', $this->_getResourceConnectionModel('core'));
            $this->loadModulesConfiguration(array('config.xml',$resourceConfig), $this);

            /**
             * Prevent local.xml directives overwriting
             */
            $mergeConfig = clone $this->_prototype;
            $this->_isLocalConfigLoaded = $mergeConfig->loadFile($this->getOptions()->getEtcDir().DS.'local.xml');
            if ($this->_isLocalConfigLoaded) {
                $this->extend($mergeConfig);
            }

            $this->applyExtends();
            Varien_Profiler::stop('config/load-modules');
            return $this;
        }
    ```
  **$this->_loadDeclaredModules();**
    Scan the `app/etc/modules` directory and collect the list of paths to all .xml files, Indicating all modules in the system.
    We form an associative array with "base" and "custom" keys. Only `Mage_All.xml` path goes to the "base" section. Modules from the
    Mageto basic pack located in `app/core/Mag` code pool "core" go to the “mage“ section “Custom“ section collects the rest of modules.
    At the end we merge everything into a single array. The data is stored in the following sequence: `Mage_All.xml`, modules with Mage namespace
    ,All other modules.

    > Mage_All.xml contains all information required for loading modules that are crucial for the proper system operation.

   All the nformation collected from the .xml files is loaded into `Mage_Core_Model_Config_Base $unsortedConfig` then we get the `$moduleDepends` array,
   based on < depends >, < active > flags and module name.
   At the end of `_loadDeclaredModules()` we create a **simpleXmlElement Object** again and merge it with the one that has been created earlier from `app/etc/*.xml`

   In the method **loadModules()**:
   ```php
    $resourceConfig = sprintf('config.%s.xml', $this->_getResourceConnectionModel('core'));
    $this->loadModulesConfiguration(array('config.xml',$resourceConfig), $this);
   ```

   `$resourceConfig` contains config.mysql4.xml line, our next step will be loading configuration from config.mysql4.xml and config.mysql4.xml files.


* **Describe class group configuration and use in factory methods**
  * Magento uses factory methods to instantiate Models, Blocks and Helpers classes, applying  a necessary method (eg: getModel, helper, etc.). You should pass and abstract name of a
  class group, followed by an entity name.
  > Class groups are described in configuratin XML files in `/etc/config.xml` files of appropiate modules.


  eg: let's try to instatiate the product Object, This can be done by using `getModel()` method.

  ```php
    $product = Mage::getModel('catalog/product');
  ```
  This method retrieves an instance of `Magento_Catalog_Product` this way:

  `app/Mage.php`

  ```php
    /**
     * Retrieve model object
     *
     * @link    Mage_Core_Model_Config::getModelInstance
     * @param   string $modelClass
     * @param   array|object $arguments
     * @return  Mage_Core_Model_Abstract|false
     */
    public static function getModel($modelClass = '', $arguments = array())
    {
        return self::getConfig()->getModelInstance($modelClass, $arguments);
    }
  ```
  **getModel()** calls **getModelInstance()** method, which gets class instance with the help of
  **getModelCalssName**

  `app/code/core/Mage/Core/Model/Config.php`
  ```php
      /**
     * Get model class instance.
     *
     * Example:
     * $config->getModelInstance('catalog/product')
     *
     * Will instantiate Mage_Catalog_Model_Mysql4_Product
     *
     * @param string $modelClass
     * @param array|object $constructArguments
     * @return Mage_Core_Model_Abstract|false
     */
    public function getModelInstance($modelClass='', $constructArguments=array())
    {
        $className = $this->getModelClassName($modelClass);
        if (class_exists($className)) {
            Varien_Profiler::start('CORE::create_object_of::'.$className);
            $obj = new $className($constructArguments);
            Varien_Profiler::stop('CORE::create_object_of::'.$className);
            return $obj;
        } else {
            return false;
        }
    }


    /**
     * Retrieve module class name
     *
     * @param   sting $modelClass
     * @return  string
     */
    public function getModelClassName($modelClass)
    {
        $modelClass = trim($modelClass);
        if (strpos($modelClass, '/')===false) {
            return $modelClass;
        }
        return $this->getGroupedClassName('model', $modelClass);
    }
  ```

    **getGroupedClassName()** method does all the work. As you can see from **getModelClassName**, we pass the group type(model, block or helper) and the class identifier
  (in our case catalog/product) to this method. It explodes our string by using '/' as needle, removes whitespaces and forms array of strings.
  Then we load `Varien_Simplexml_Element` and pass our group name (catalog) to find the class prefix name from `config.xml` of our module. In our example it wold be `Mage_Catalog_Model`;
  in case this extension was overwritten, it could be `Namespace_Catalog_Model`.

  We also add entity class name(product) and after using Magento `uc_words` based on PHP ucwords that return a string with the first character of each word capitalized, if that character
  is alphanumeric. And finally receive `Mage_Catalog_Model_Product` that will be returned in `getModelInstance`

  `app/code/core/Mage/Core/Model/Config.php`

  ```php
     /**
       * Retrieve class name by class group
       *
       * @param   string $groupType currently supported model, block, helper
       * @param   string $classId slash separated class identifier, ex. group/class
       * @param   string $groupRootNode optional config path for group config
       * @return  string
       */
      public function getGroupedClassName($groupType, $classId, $groupRootNode=null)
      {
          if (empty($groupRootNode)) {
              $groupRootNode = 'global/'.$groupType.'s';
          }

          $classArr = explode('/', trim($classId));
          $group = $classArr[0];
          $class = !empty($classArr[1]) ? $classArr[1] : null;

          if (isset($this->_classNameCache[$groupRootNode][$group][$class])) {
              return $this->_classNameCache[$groupRootNode][$group][$class];
          }

          $config = $this->_xml->global->{$groupType.'s'}->{$group};

          // First - check maybe the entity class was rewritten
          $className = null;
          if (isset($config->rewrite->$class)) {
              $className = (string)$config->rewrite->$class;
          } else {
              /**
               * Backwards compatibility for pre-MMDB extensions.
               * In MMDB release resource nodes <..._mysql4> were renamed to <..._resource>. So <deprecatedNode> is left
               * to keep name of previously used nodes, that still may be used by non-updated extensions.
               */
              if (isset($config->deprecatedNode)) {
                  $deprecatedNode = $config->deprecatedNode;
                  $configOld = $this->_xml->global->{$groupType.'s'}->$deprecatedNode;
                  if (isset($configOld->rewrite->$class)) {
                      $className = (string) $configOld->rewrite->$class;
                  }
              }
          }

          // Second - if entity is not rewritten then use class prefix to form class name
          if (empty($className)) {
              if (!empty($config)) {
                  $className = $config->getClassName();
              }
              if (empty($className)) {
                  $className = 'mage_'.$group.'_'.$groupType;
              }
              if (!empty($class)) {
                  $className .= '_'.$class;
              }
              $className = uc_words($className);
          }

          $this->_classNameCache[$groupRootNode][$group][$class] = $className;
          return $className;
      }
  ```


  * **Helpers**
    * Istantiate a helper class the same way  you do models.
    We call `Mage::helper('group/entity') and it calls `getHelperClassName()` this method has a default
    value 'data' in entity name. It means that if you pass only a group name, for eg: `Mage::helper('catalog'),
    it will create an object of `Mage_Catalog_Helper_Data` class.

      `app/Mage.php`
      ```php
       /**
       * Retrieve helper object
       *
       * @param string $name the helper name
       * @return Mage_Core_Helper_Abstract
       */
      public static function helper($name)
      {
          $registryKey = '_helper/' . $name;
          if (!self::registry($registryKey)) {
              $helperClass = self::getConfig()->getHelperClassName($name);
              self::register($registryKey, new $helperClass);
          }
          return self::registry($registryKey);
      }
      ```
      `app/code/core/Mage/Core/Model/Config.php`
      ```php
       /**
       * Retrieve helper class name
       *
       * @param   string $name
       * @return  string
       */
      public function getHelperClassName($helperName)
      {
          if (strpos($helperName, '/') === false) {
              $helperName .= '/data';
          }
          return $this->getGroupedClassName('helper', $helperName);
      }
      ```
  * **Blocks**
    * The process of instantianting blocks is similar to the one of creating models and helpers. **Blocks** can be instantiated via layout xml

      `Namespace_Modulename_Block`

      ```php
         Mage_Core_Model_Layout::createBlock(...);
      ```

*  **Describe the process and configuration of class overrides in Magento**
  I recomend to rewrite a method using the Inheritance

   **How to do it?**
  Creating a module. In config.xml inside globals-> models, blocks and helpers we can specify our rewrites.
  eg: to override the standard class of the `Mage_Catalog_Model_Product` product, you need to use:

    ```xml
      <global>
        ...
        <models>
          ...
          <catalog>
            <rewrite>
              <product> Namespace_Modulename_Model_MyProduct </product>
            </rewrite>
          </catalog>
        </models>
      </global>
    ```

    Principle is the same as in Magento path.

    `<catalog>`  Specify module namespace

    `<rewrite>`  mark that next list of modules rewrites will be described;

    `<product>` Part of the classname following the group classname(`Mage_Catalog_Model`). A new class name is specified inside.
    And the class will be used isntead of the standard one.

    To override the `Mage_Adminhtml_Sales_Order_Create` class, we will add the following lines in configuration (models section).
    ```xml
      <adminhtml>
          <rewrite>
            <sales_order_create>Namespace_Modulename_Adminhtml_Order_Create</sales_order_create>
          </rewrite>
      </adminhtml>
    ```
  * **Controller**
    * To override a controller, we add a new router to config.xml of the module eg:
    ```xml
      <frontend>
        <routers>
          <modulename>
            <use>standard</use>
            <args>
              <module>Namespace_Modulename</module>
              <frontName>routername</frontName>
            </args>
          </modulename>
        </routes>
      </frontend>
    ```
    Normally we set the connection between our router and a module. In this case we override the connection of existing router with our mudule.

    ```xml
      <frontend>
        <routers>
          <prevmodulename>
            <args>
              <modules>
                <namespace_modulename before="Prevnamespace_Prevmodulename">
                  Namespace_Modulename
                  </namespace_modulename>
              </modules>
            </args>
          </prevmodulename>
        </routers>
      </frontend>
    ```
    Next we create a controller with the same name in our module like this:
    ```php
      <?php
        require_once 'Prevnamespace/Prevmodulename/controllers/FilenameController.php';
        class Namespace_Modulename_FilenameController extends Prevnamespace_Prevmodulename_FilenameController
        {
          ...
        }
    ```

  * **Register an Observer**
    * **Observer Pattern**, implemented in Magento, allows inserting additional actions at given moments of code execution
    > Events will be declared in config.xml via layout. In our case:

    ```xml
      <adminhtml>
        <events>
          <catalog_product_save_after>
            <observers>
              <my_individual_name>
                <type> sigleton </type>
                <class>modulename/observer</class>
                <method>methodName</method>
              </my_individual_name>
            </observers>
          </catalog_product_save_after>
        <events>
      </adminhtml>
    ```
    > In this case I announce the event for area adminhtml, but events may also be described both by **global** and of course **frontend**

    ```php
      class Namespace_Module_Module_Observer
      {
        public function methodName(Varien_Event_Observer $observer)
        {
          ...
        }
      }
    ```

    We can get the data of the array, transfered in `dispatchEvent` method, via `$observer` object
    We are free to use the ones, formed automatically and invidually for your module for models and controllers

    * Models

      * *_load_before
      * *_load_after
      ---
      * *_save_before
      * *_save_after
      * *_save_commit_after
      ---
      * *_delete_before
      * *_delete_after
      * *_delete_commit_after
      ---
      * - protected $_eventPrefix='' `Specified individually in a created model`
    * Controllers
      * controller_action_predispatch_*
      * controller_action_predispatch_**
      ---
      * controller_action_postdispatch_*
      * controller_action_postdispatch_**
      ---
      * controller_action_layout_render_before_** `described in a controller, it mostly concerns blocks`
      ---
      * - RouteName
      * - RouteName_ControllerName_ActionName

  * **Set up a cron job**
    * Here is how config of our custom extension will look like.
      ```xml
        <config>
          <crontab>
            <jobs>
              <cronjob_code>
                <scheudule><cron_expr>*/15 * * * *</cron_expr></schedule>
                <run><model>modulename/modelname:methodName</model></run>
              </cronjob_code>
            </jobs>
          </crontab>
        </config>
      ```

      `<cronjob_tab>` Should be unique identifier, will be used as job_code in cron_schedule DB table.

      `<schedule><cron_expr>* * * * *</cron_expr></schedule>` Each asterisk stands for time period corresponding to: minutes, hours, days, months, years.
      If we leave it this way(* * * * *), cron job will executed every minute. in our previous example the cron job will be executed every 15 minutes.

      `<schedule><cron_expr>*/15 * * * *</cron_expr></schedule>` or we can use alternative syntax to do that.`<schedule><cron_expr>0,15,30,45****</cron_expr></schedule>`.

      `<run><model>modulename/modelname:methodName</model></run>` your modulename and modelname should be written in lowercase and stored according to certain(`Magento Factory Method`) rules.
      `methodName` should be written as is.

      >We need to write the code of our method in `app/code/local/Namespace/Modulename/Model/Modelname.php`

      ```php
        <?php
          public function methodName(){
          // your logic here.
          }
      ```
  * Magento XML DOM configuration-based
    * **How does the framework discover active modules and their configuration?**
      `Mage_Core_Model_Config::_loadDeclaredModules()`

    * **What are the common methods with which the framework accesses its configuration values and areas?**
    ```php
      Mage_Core_Model_Config::getDistroServerVars (Get default server variables values ),
      Magento_Core_Model_Config::getStoresConfigByPath(Retrieve store Ids for $path with checking ),
      Magento_Core_Model_Config::$_eventAreas( Configurationfor events by area)
    ```
