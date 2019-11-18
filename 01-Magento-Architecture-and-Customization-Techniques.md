# 1. Magento Architecture and Customization Techniques 

## 1.1 Describe the Magento module-based architecture

### Describe module architecture.

> A module is a **logical group** – that is, a directory containing blocks, controllers, helpers, models – that are related to a **specific business feature**. In keeping with Magento’s commitment to optimal modularity, a module encapsulates one feature and has minimal dependencies on other modules. [Magento DevDocs - Module overview - What is](https://devdocs.magento.com/guides/v2.3/architecture/archi_perspectives/components/modules/mod_intro.html#arch-modules-overview)
>
> The purpose of a module is to provide specific product features by implementing new functionality or extending the functionality of other modules. Each module is designed to function independently, so the inclusion or exclusion of a particular module does not typically affect the functionality of other modules. [Magento DevDocs - Module overview - Purpose](https://devdocs.magento.com/guides/v2.3/architecture/archi_perspectives/components/modules/mod_intro.html#purpose-of-a-module)


Modules are usually located in Magento 2 vendor directory. The vendor directory has the format: `vendor/<vendor>/<type>-<module-name>`. Where `<type>` is module, theme or language. 

In general, modules add functionality and themes customize the appearance. Both modules and themes have their own life cycle, which allows you to install, disable and delete the elements.

#### Magento area types

`adminhtml` - The Admin panel area includes the code needed for store management. The /app/design/adminhtml directory contains all the code for components you’ll see while working in the Admin panel.

`frontend` - The storefront (or frontend) contains template and layout files that define the appearance of your storefront.
base - used as a fallback for files absent in adminhtml and frontend areas.

`crontab` - In pub/cron.php, the \Magento\Framework\App\Cron class always loads the ‘crontab’ area.

`webapi_rest` - The REST area has a front controller that understands how to do URL lookups for REST-based URLs.

`webapi_soap` – The SOAP area.

#### Module/area interaction guidelines:

Modules should not depend on other modules’ areas. Disabling an area does not result in disabling the modules related to it. Areas are registered in the Dependency Injection framework di.xml file.

#### Magento request processing

After the area name, the URI segment specifies the frontname. When an HTTP request arrives, Magento extracts the handle from the URL and interprets it as follows: `[frontName]/[controller folder]/[controller class]` where frontName is a value defined in the module. For example, in `catalog/product/view`, catalog is the name of the module used, product is the controller folder, and view is the controller class. For deeper directory structures, the controller folders are separated with _ (for example, `catalog/product_compare/add` for `Magento/Catalog/Controller/Product/Compare/Add.php`).

Note that only the execute() method of any given controller is executed.

#### Module dependencies and limitations

> A software dependency identifies one software component’s reliance on another for proper functioning. A core principle of Magento architecture is the **minimization of software dependencies**. Instead of being closely interrelated with other modules, modules are optimally designed to be loosely coupled. Loosely coupled modules require little or no knowledge of other modules to perform their tasks.

A module can have the following relations with others:
> - **uses:** module A uses module B;
> - **reacts to:** module A reacts to module B if its behavior is triggered by an event in module B (without the knowledge of module B about module A);
> - **customizes:** module A customizes module B in case it changes the behavior of module B;
> - **implements:** module A implements module B if it implements any behavior part defined in module B;
> - **replaces:** module A replaces module B if it provides its own API version, which is opened and implemented by module B.
> [Reference](https://amasty.com/blog/magento-2-certification-module-based-architecture/)

About the module dependency, in Magento 2 there are 2 types of module dependencies: hard and soft. Modules with a hard dependency can’t function without the modules they depend on. Where modules with soft dependency can function independently.

Hard dependencies are definited in `require` section of `app/code/<Vendor>/<Module>/composer.json`.

Soft dependencies are definited in `suggest` section of `app/code/<Vendor>/<Module>/composer.json`.
This also definited in `<sequence>` node of `app/code/<Vendor>/<Module>/etc/module.xml` file.

```xml
<sequence>
  <module name="Vendor_Module"/>
</sequence>
```


### What are the significant steps to add a new module?

#### 1. Declare component dependencies in composer.json.

Magento-specific package types: magento2-module for modules, magento2-theme for themes, magento2-language for language packages, magento2-component for general extensions that do not fit any of the other types.


#### 2. Register the component using the registration.php file

- Register modules: ComponentRegistrar::register(ComponentRegistrar::MODULE, '<VendorName_ModuleName>', __DIR__);
- Themes: ComponentRegistrar::register(ComponentRegistrar::THEME, '<area>/<vendor>/<theme name>', __DIR__);
- Language packages: ComponentRegistrar::register(ComponentRegistrar::LANGUAGE, '<VendorName>_<packageName>', __DIR__);
- Libraries: ComponentRegistrar::register(ComponentRegistrar::LIBRARY, '<vendor>/<library_name>', __DIR__);

**registration.php load flow:** [Fonte](https://github.com/magento-notes/magento2-exam-notes/blob/master/1.%20Magento%20Architecture%20and%20Customization%20Techniques/1.%20Describe%20Magento%E2%80%99s%20module-based%20architecture.md#registrationphp-search-paths)
1. pub/index.php
2. app/bootstrap.php
3. app/autoload.php
4. vendor/autoload.php
5. vendor/module[]/registration.php -- last step in Composer init
6. `Magento\Framework\Component\ComponentRegistrar::register(type='module', name='Prince_Productattach', path='/var/www/...')`

#### 3. Name, declare and define dependences in module.xml file.

Each module should be named and declared in a component-specific XML definition file: module.xml.
- module.xml (modules), theme.xml (themes) and language.xml (for language packages).

For the modules, the module.xml file is located in the `<vendor>/<module>/etc` directory. This file also lists the module dependencies.
Dependencies are loaded in the `<sequence>` element:

```xml
<sequence>
  <module name="Vendor_Module"/>
</sequence>
```


### What are the different Composer package types?
Use the following format when naming your package: `<vendor-name>/<package-name>`

`vendor-name`: All letters in the vendor name must be in lowercase. For example, the vendor name format for extensions released by Magento Inc is magento.

`package-name`: All letters in the package-name must be in lowercase. The convention for Magento package names is the following `magento/<type-prefix>-<suffix>`

  `<type-prefix>` is any of the Magento extension types: module- for module extensions, theme- for theme extensions, language- for language extensions, product- for metapackages such as Magento Open Source or Magento Commerce
autoload - Specify necessary information to be loaded, such as registration.php.
```json
{
 "name": "Acme-vendor/bar-component",
 "autoload": {
    "psr-4": { "AcmeVendor\\BarComponent\\": "" },
    "files": [ "registration.php" ]
 }
}
```

### When would you place a module in the app/code folder versus another location?

Modules are located in **vendor** directory or **app/code** directory. 
- The vendor directory contains Magento core modules and modules installed with Composer.
- The app directory is the recommended location for component development




## 1.2 Describe the Magento directory structure 
Describe the Magento directory structure. What are the naming conventions, and how are namespaces established? How can you identify the files responsible for some functionality? 

## 1.3 Utilize configuration and configuration variables scope 
Determine how to use configuration files in Magento. Which configuration files are important in the development cycle? 
Describe development in the context of website and store scopes. How do you identify the configuration scope for a given variable? How do native Magento scopes (for example, price or inventory) affect development and decision-making processes? 
Demonstrate an ability to add different values for different scopes. How can you fetch a system configuration value programmatically? How can you override system configuration values for a given store using XML configuration?

## 1.4 Demonstrate how to use dependency injection (DI) 
Demonstrate the ability to use the dependency injection concept in Magento development. How are objects realized in code? Why is it important to have a centralized object creation process?
Identify how to use DI configuration files for customizing Magento. How can you override a native class, inject your class into another object, and use other techniques available in di.xml (for example, virtualTypes)? 
Given a scenario, determine how to obtain an object using the ObjectManager object. How would you obtain a class instance from different places in the code? 

## 1.5 Demonstrate ability to use plugins 
Demonstrate an understanding of plugins. How are plugins used in core code? How can they be used for customizations? 

## 1.6 Configure event observers and scheduled jobs 
Demonstrate how to create a customization using an event observer. How are observers registered? How are they scoped for frontend or backend? How are automatic events created, and how should they be used? How are scheduled jobs configured?

## 1.7 Utilize the CLI 
Describe the usage of bin/magento commands in the development cycle. Which commands are available? How are commands used in the development cycle? 

## 1.8 Describe how extensions are installed and configured 
How would you install and verify an extension by a customer’s request?

