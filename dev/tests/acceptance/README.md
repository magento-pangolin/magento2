## Magento Functional Test Framework
Welcome to the new Magento Functional Tests! We're excited for you to try out the new Magento Test Framework(MFTF), a brand new, testing framework allowing test developers to write flexible, re-usable, quality tests.

## Built With
* [Codeception](http://codeception.com/)
* [Robo](http://robo.li/)
* [Allure](http://allure.qatools.ru/)

## Prerequisites
* [PHP v7.x](http://php.net/manual/en/install.php)
* [Composer v1.4.x](https://getcomposer.org/download/)
* [Java](https://www.java.com/en/download/)
* [Selenium Server](http://selenium-release.storage.googleapis.com/index.html/)
* [ChromeDriver](https://chromedriver.storage.googleapis.com/index.html?path=2.33/)
* [Allure CLI](https://docs.qameta.io/allure/latest/#_installing_a_commandline)
* [GitHub](https://desktop.github.com/)
* Configure Magento for [Automated Testing](http://devdocs.magento.com/guides/v2.0/mtf/mtf_quickstart/mtf_quickstart_magento.html)

### Recommendations
* We recommend using [PHPStorm 2017](https://www.jetbrains.com/phpstorm/) for your IDE. They recently added support for [Codeception Test execution](https://blog.jetbrains.com/phpstorm/2017/03/codeception-support-comes-to-phpstorm-2017-1/) which is helpful when debugging.
* We also recommend updating your [$PATH to include](https://stackoverflow.com/questions/7703041/editing-path-variable-on-mac) `vendor/bin` so you can easily execute the necessary `robo` and `codecept` commands instead of `vendor/bin/robo` or `vendor/bin/codecept`.  

----

# Installation
You can set up the repo via this git repository.
```
git clone https://github.com/magento-pangolin/magento2
cd magento2/dev/tests/acceptance
composer install
```

----

# Robo
Robo is a task runner for PHP that allows you to alias long complex CLI commands to simple commands, we use it extensively in the framework.

----

# Building The Framework
After installing the dependencies you will want to build the Codeception project, which is a dependency of the test runner. Exceute `robo build:project` to complete this task.

`robo build:project`

----

# Generate PHP files for Tests
All Tests in the Framework are written in XML and need to have the PHP generated for Codeception to run. Run the following command to generate the PHP files into the following directory: `tests/acceptance/Magento/FunctionalTest/_generated`

If this directory doesn't exist it will be created.

`robo generate:tests`

----

# Running Tests
## Start the Selenium Server
PLEASE NOTE: You will need to have an instance of the Selenium Server running on your machine before you can execute the Tests.

```
cd [LOCATION_OF_SELENIUM_JAR]
java -jar selenium-server-standalone-X.X.X.jar
```

## Run Tests Manually
You can run the Codeception tests directly without using Robo if you'd like. To do so please run `codecept run functional` to execute all Functional tests that DO NOT include @env tags. IF a Test includes an [@env tag](http://codeception.com/docs/07-AdvancedUsage#Environments) you MUST include the `--env ENV_NAME` flag.

#### Common Codeception Flags:

* --env
* --group
* --skip-group
* --steps
* --verbose
* --debug
  * [Full List of CLI Flags](http://codeception.com/docs/reference/Commands#Run)

#### Examples

* Run ALL Functional Tests without an @env tag: `codecept run functional`
* Run ALL Functional Tests without the "skip" @group: `codecept run functional --skip-group skip`
* Run ALL Functional Tests with the @group tag "example" without the "skip" @group tests: `codecept run functional --group example --skip-group skip`

## Run Tests using Robo
* Run all Functional Tests using the @env tag "chrome": `robo chrome`
* Run all Functional Tests using the provided @group tag: `robo group GROUP_NAME`
* Run all Functional Tests listed under the provided Folder Path: `robo folder tests/acceptance/Magento/FunctionalTest/MODULE_NAME`

----

# Allure Reports
You can run the following commands in the Terminal to generate and open an Allure report.

* Build the Report: `allure generate tests/_output/allure-results/ -o tests/_output/allure-report/`
* Open the Report: `allure report open --report-dir tests/_output/allure-report/`

----


## Available Robo Commands
You can see a list of all available Robo commands by calling `robo` directly in the Terminal.

* `robo`
  * Lists all available Robo commands.

----

# Troubleshooting
* TimeZone Error - http://stackoverflow.com/questions/18768276/codeception-datetime-error
* TimeZone List - http://php.net/manual/en/timezones.america.php
* System PATH - Make sure you have `vendor/bin/` and `vendor/` listed in your system path so you can run the  `codecept` and `robo` commands directly:

    `sudo nano /etc/private/paths`
    
* StackOverflow Help: https://stackoverflow.com/questions/7703041/editing-path-variable-on-mac
* Allure `@env error` - Allure recently changed their Codeception Adapter that breaks Codeception when tests include the `@env` tag. A workaround for this error is to revert the changes they made to a function. 
    * Locate the `AllureAdapter.php` file here: `vendor/allure-framework/allure-codeception/src/Yandex/Allure/Adapter/AllureAdapter.php`
    * Edit the `_initialize()` function found on line 77 and replace it with the following:
```
public function _initialize(array $ignoredAnnotations = [])
    {
        parent::_initialize();
        Annotation\AnnotationProvider::registerAnnotationNamespaces();
        // Add standard PHPUnit annotations
        Annotation\AnnotationProvider::addIgnoredAnnotations($this->ignoredAnnotations);
        // Add custom ignored annotations
        $ignoredAnnotations = $this->tryGetOption('ignoredAnnotations', []);
        Annotation\AnnotationProvider::addIgnoredAnnotations($ignoredAnnotations);
        $outputDirectory = $this->getOutputDirectory();
        $deletePreviousResults =
            $this->tryGetOption(DELETE_PREVIOUS_RESULTS_PARAMETER, false);
        $this->prepareOutputDirectory($outputDirectory, $deletePreviousResults);
        if (is_null(Model\Provider::getOutputDirectory())) {
            Model\Provider::setOutputDirectory($outputDirectory);
        }
    }
```
