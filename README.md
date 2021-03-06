# Creating Integration Server

Follow the below steps to create the integration server
1. Run the Vagrant file
2. You can see that following task are done by the vagrant file
  * The VM is automatically provisioned using Ansible playbooks.
  * These playbooks install GitLab and Docker.
  * GitLab is used as VCS and CI, whereas Docker is used to handle the integration environments.
  * We have downloaded the katalon Runtime Engine and unzipped it in the home directory, changed 
  * Downloaded JavaRunTime environment to run the TestNG automation test cases
3. Follow instruction in tutorial https://github.com/acapozucca/devops/tree/master/pipeline/s2-automate-build to setup our integration server.

4. Follow tutorial in https://github.com/venkateshwarant/UAT to Create a product and write automation for our product and integrate those in our integration server pipeline.


## NOTES: Integrating testNG testcases in pipeline

### To run automation in the remote machine from the integration server
     * we dont need to install the browsers, driver binaries and xvfb in CI/CD server
     * Also note that both the CI/CD server, stage-VM and Selenium-grid should be in same network
     * below is the syntax to create a remote driver in selenium

```
 final ChromeOptions chromeOptions = new ChromeOptions();
	chromeOptions.addArguments("--headless");
	chromeOptions.addArguments("--no-sandbox");
	chromeOptions.addArguments("--disable-dev-shm-usage");
	chromeOptions.addArguments("--window-size=1200x600");
 chromeOptions.setBinary("/usr/bin/google-chrome");
	DesiredCapabilities capability1 = DesiredCapabilities.chrome();
	capability1.setBrowserName("chrome");
	capability1.setPlatform(Platform.LINUX);
	capability1.setCapability(ChromeOptions.CAPABILITY, chromeOptions);
	WebDriver driver = new RemoteWebDriver(new URL("http://192.168.33.13:4444/wd/hub"), capability1);
```
Where http://192.168.33.13:4444/wd/hub is the Selenium hub server URL

   * We run automation for the product deployed in stage-vm as below
   
```

driver.get("http://192.168.33.10/helloworld.html");

```
where 192.168.33.10 is the IP Address of the stage VM


### To run automation in the CI/CD server itself
     * we need to install the browsers, driver binaries and xvfb in CI/CD server
     
#### Pipeline file for testNG test cases would be like

```

image: maven:latest
stages:
  - deploy
  - test
cache:
  paths:
    - target/
test_app:
  stage: test
  script:
    - mvn test
deploy:
    stage: deploy
    tags:
    - stage-vm-shell
    script:
    - cp src/main/java/Tutorial1/helloworld.html /home/vagrant/stage
    
```
     
## NOTES: Integrating katalon testcases in pipeline
* you have to add the katalon licence file which you have bought inside the runtime engine manually, only the you can integrate the katalon test cases with the integration server
     
#### Pipeline file for Katalon test cases would be like

```
run_katalon_test_suite:
      tags:
        - shell
      script:
        -  export DISPLAY=:0
        - /home/vagrant/Katalon_Studio_Engine_Linux_64-7.0.10/katalonc -noSplash -runMode=console -projectPath="/home/gitlab-runner/builds/nbb5yDmB/0/gitlab/venkateshwaran/uat_katalon/UAT_Katalon.prj"  -retry=0 -testSuitePath="Test Suites/HelloWorldTestSuite" -executionProfile="default" -browserType="Chrome (headless)" -apiKey="1dd161fc-ec7c-4215-a9f4-2611752d646e" --config -webui.autoUpdateDrivers=true

```

* note that here we are invoking our test cases using katalon engine
* we have also mentioned that test cases should run in chrome browser headlessly
