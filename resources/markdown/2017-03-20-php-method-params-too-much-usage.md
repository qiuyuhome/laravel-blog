---
title: PHP方法中参数太多, 怎么写呢?
description: 
keywords: PHP
create_time: 2017-03-20
tag: PHP
category: 笔记
---

PHP方法中参数太多, 怎么写呢? 今天看到了一个格式, 感觉非常不错哦. 就记录一下. 大家可以参考一下. 

今天看谷歌开源的一个项目中, 看到了这样的写法, 可以看到, 把参数写成了多行. 这样看起来就舒爽多了. 是个好办法, 以前都是写一行的. 看起来很不爽.


```php
   /**
     * Construct the RemoteWebDriver by a desired capabilities.
     *
     * @param string $selenium_server_url The url of the remote Selenium WebDriver server
     * @param DesiredCapabilities|array $desired_capabilities The desired capabilities
     * @param int|null $connection_timeout_in_ms Set timeout for the connect phase to remote Selenium WebDriver server
     * @param int|null $request_timeout_in_ms Set the maximum time of a request to remote Selenium WebDriver server
     * @param string|null $http_proxy The proxy to tunnel requests to the remote Selenium WebDriver through
     * @param int|null $http_proxy_port The proxy  port to tunnel requests to the remote Selenium WebDriver through
     * @param DesiredCapabilities $required_capabilities The required capabilities
     * @return RemoteWebDriver
     */
    public static function create(
        $selenium_server_url = 'http://localhost:4444/wd/hub',
        $desired_capabilities = null,
        $connection_timeout_in_ms = null,
        $request_timeout_in_ms = null,
        $http_proxy = null,
        $http_proxy_port = null,
        DesiredCapabilities $required_capabilities = null
    ) {
        $selenium_server_url = preg_replace('#/+$#', '', $selenium_server_url);

        $desired_capabilities = self::castToDesiredCapabilitiesObject($desired_capabilities);

        $executor = new HttpCommandExecutor($selenium_server_url, $http_proxy, $http_proxy_port);
        if ($connection_timeout_in_ms !== null) {
            $executor->setConnectionTimeout($connection_timeout_in_ms);
        }
        if ($request_timeout_in_ms !== null) {
            $executor->setRequestTimeout($request_timeout_in_ms);
        }

        if ($required_capabilities !== null) {
            // TODO: Selenium (as of v3.0.1) does accept requiredCapabilities only as a property of desiredCapabilities.
            // This will probably change in future with the W3C WebDriver spec, but is the only way how to pass these
            // values now.
            $desired_capabilities->setCapability('requiredCapabilities', $required_capabilities->toArray());
        }

        $command = new WebDriverCommand(
            null,
            DriverCommand::NEW_SESSION,
            ['desiredCapabilities' => $desired_capabilities->toArray()]
        );

        $response = $executor->execute($command);
        $returnedCapabilities = new DesiredCapabilities($response->getValue());

        $driver = new static($executor, $response->getSessionID(), $returnedCapabilities);

        return $driver;
    }
```




