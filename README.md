# Robot Framework Docker Support

> Why choose the docker [edragta/robotframework](https://hub.docker.com/r/edragta/robotframework/) for Robot Framework: 
> 1. With no VNC supports, we could debug our script easier and more flexible if any test failure occurs during the CI/CD
![Screenshot](noVPC_Sample.png)
> 2. Support Python 2.7, and Python 3.6 for Robot Framework, such as robot, robot2, robot3
> 3. Installed browsers, such as Chrome, Firefox, PhantomJS for testing
> 4. We are working on a FREE workshop for Robot Framework

## Content
- [Build](#build)
- [Supports](#supports)
- [User Docker Step by Step](#use-docker-step-by-step)
- [Use Docker Directly](#use-docker-directly)
- [Jenkins Integration](Jenkins)
- [Proxy](#proxy)
- [Useful Links](#useful-links)

## Build
Get the source and sample from git hub [edragta/robotframework-docker](https://github.com/edragta/robotframework-docker), and build image: `docker build -t robotframework .`
> Note: In Dockerfile, you could set certain certain Chrome/ChromeDriver/Firefox/Geckodriver version

Or, pull the image from docker hub - [edragta/robotframework](https://hub.docker.com/r/edragta/robotframework/): `docker pull edragta/robotframework`

## Supports
Support the Robot Framework based on Python2.7 & Python 3.6.

With No VPC support, we could debug the scripts more flexible.

**1. Python 2.7**

- **Packages**

    > **Note:** Suggest to use robotframework-seleniumlibrary for testing
    
    - [robotframework 3.0.3](https://pypi.org/project/robotframework/)
    - [robotframework-seleniumlibrary 3.1.1](https://pypi.org/project/robotframework-seleniumlibrary/)
    - [robotframework-selenium2library 1.8.0](https://pypi.org/project/robotframework-selenium2library/1.8.0/)
    - [robotframework-pabot 0.44](https://pypi.org/project/robotframework-pabot/)

- **Robot Command**

    - `robot --version`
    - `robot2 --version`
    - `pybot --version`
    - `pybot2 --version`
    - `rebot --version`
    - `rebot2 --version`
    - `pabot --version`
    - `pabot2 --version`

**2. Python 3.6**

- **Packages**

    - [robotframework 3.0.3](https://pypi.org/project/robotframework/)
    - [robotframework-seleniumlibrary 3.1.1](https://pypi.org/project/robotframework-seleniumlibrary/)
    - [robotframework-pabot 0.44](https://pypi.org/project/robotframework-pabot/)

- **Robot Command**

    - `robot3 --version`
    - `pybot3 --version`
    - `rebot3 --version`
    - `pabot3 --version`

**3. Browsers**

- Chrome v65.0.3325.181 - ChromeDriver v2.36
- Firefox v59.0.3 - Geckodriver v0.20.1
- PhantomJS v2.1.1 

## Use Docker Step by Step
**1. Assuming that you have copied the "Sample" folder to the ~/Debug to assign the local path ~/Debug to docker path /tmp, and start the docker container**
   ```
   docker run \
        -v ~/Debug:/tmp \
        --rm \
        -p 6901:6901 \
        -p 5901:5901 \
        --shm-size 2048m \
        -d \
        edragta/robotframework:latest
   ```
   
   
**2. Access the [http://localhost:6901/?password=vncpassword/](http://localhost:6901/?password=vncpassword/) to the noVNC Env**
![Screenshot](noVPC_Sample.png)

**3. Open terminal and cd to your folder: cd /tmp/Sample**

**4. Run Test**

- **Run Test (use default browser - Chrome)**

    ```
    # Python 2.7
    robot --outputdir Reports/RunTest sample_1.robot
    
    # Python 3.6
    robot3 --outputdir Reports/RunTest sample_1.robot
    ```


- **Run Parallel Test (use browser - Firefox)**

    ```
    # Python 2.7
    pabot --processes 2 --outputdir Reports/RunParallelTest --variable BROWSER:Firefox *.robot
    
    # Python 3.6
    pabot3 --processes 2 --outputdir Reports/RunParallelTest --variable BROWSER:Firefox *.robot
    ```


- **Run Cross Browsers Test**

    ```
    # Python 2.7
    pabot --argumentfile1 firefox.args --argumentfile2 chrome.args --processes 4 --outputdir Reports/RunCrossBrowserTest *.robot
    
    # Python 3.6
    pabot3 --argumentfile1 firefox.args --argumentfile2 chrome.args --processes 4 --outputdir Reports/RunCrossBrowserTest *.robot
    ```

## Use Docker Directly
**1. Assume you have copied the folder "Sample" to the ~/Debug**

**2. Run Test (python 2.7 as reference)**

- **Run Test (use default browser - Chrome)**

    ```
    docker run \
        -v ~/Debug:/tmp \
        --rm \
        -p 6901:6901 \
        -p 5901:5901 \
        --shm-size 2048m \
        edragta/robotframework:latest \
        /bin/bash -c "cd Sample; robot --outputdir Reports/RunTest sample_1.robot"
    ```
    
    
- **Run Parallel Test (use browser - Firefox)**

    ```
    docker run \
        -v ~/Debug:/tmp \
        --rm \
        -p 6901:6901 \
        -p 5901:5901 \
        --shm-size 2048m \
        edragta/robotframework:latest \
        /bin/bash -c "cd Sample; pabot --processes 2 --outputdir Reports/RunParallelTest --variable BROWSER:Firefox *.robot"
    ```


- **Run Cross Browsers Test**

    ```
    docker run \
        -v ~/Debug:/tmp \
        --rm \
        -p 6901:6901 \
        -p 5901:5901 \
        --shm-size 2048m \
        edragta/robotframework:latest \
        /bin/bash -c "cd Sample; pabot --argumentfile1 firefox.args --argumentfile2 chrome.args --processes 4 --outputdir Reports/RunCrossBrowserTest *.robot"
    ```

## Proxy
**1. Chrome/Firefox/IE/Safari: Use desired_capabilities to pass proxy**

``` 
${proxy}=  Evaluate  {'proxy': {'proxyType': 'manual', 'httpProxy': '172.17.0.1:3128', 'sslProxy': '172.17.0.1:3128'}}
open browser  http://robotframework.org/  Chrome  desired_capabilities=${proxy}
```
  
**2. Chrome: Alternatively, use keyword: Open Chrome With Proxy**

``` 
Open Chrome With Proxy
    [arguments]  ${url}
    ${chrome_proxy}=  Evaluate  "--proxy-server=172.17.0.1:3128"
    ${options}=  Evaluate  sys.modules['selenium.webdriver'].ChromeOptions()  sys, selenium.webdriver
    Call Method  ${options}  add_argument  ${chrome_proxy}
    Create WebDriver  Chrome  chrome_options=${options}
    go to  ${url}
```
  
**3. Firefox: Alternatively, use firefox profile: /headless/ta.default**

``` 
# Please update the proxy in /headless/ta.default/prefs.js accordingly
open browser  http://robotframework.org/  Firefox  ff_profile_dir=/headless/ta.default
```
   
> Note: You could use sed to update, for example, 172.17.0.1 to 127.0.0.1

`sed -i 's/172.17.0.1/127.0.0.1/g' /headless/ta.default/prefs.js`
  
**4. PhantomJS: Selenium support is deprecated. To pass proxy, use keyword: Open PhantomJS With Proxy**

``` 
Open PhantomJS With Proxy
    [arguments]  ${url}
    ${service_args}=  Evaluate  ["--proxy=172.17.0.1:3128", "--ignore-ssl-errors=yes"]
    Create WebDriver  PhantomJS  service_args=${service_args}
    go to  ${url}
```

## Useful Links
1. [Robot Build In Library](http://robotframework.org/robotframework/#standard-libraries)
2. [Selenium Library](http://robotframework.org/SeleniumLibrary/SeleniumLibrary.html)
3. [All Possible Library](http://robotframework.org/robotframework/#standard-libraries)
4. [GeckoDriver](https://github.com/mozilla/geckodriver/releases)
5. [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/)
6. [PhantomJS](http://phantomjs.org/download.html)
7. [Previous Chrome RPM Package](http://orion.lcg.ufrj.br/RPMS/myrpms/google/)
8. [Previous Firefox](https://ftp.mozilla.org/pub/mozilla.org/firefox/releases/)
