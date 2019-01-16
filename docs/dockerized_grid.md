## Install docker and docker compose
An installation guide can be found under this link [docer compose](https://docs.docker.com/compose/install/)

## Dockerized selenium grid setup:
1. Copy below content and save it in your project root directory as **docker-compose.yaml** file 

**docker-compose.yaml**
```yaml
# To execute this docker-compose yml file use `docker-compose -f <file_name> up`
# Add the `-d` flag at the end for detached execution
version: "3"
services:
  selenium-hub:
    image: selenium/hub:3.141.59-dubnium
    container_name: selenium-hub
    ports:
      - "4444:4444"
  chrome:
    image: selenium/node-chrome:3.141.59-dubnium
    volumes:
      - /dev/shm:/dev/shm
    depends_on:
      - selenium-hub
    ports:
      - "5800:5800"
    environment:
      - HUB_HOST=selenium-hub
      - HUB_PORT=4444
  firefox:
    image: selenium/node-firefox:3.141.59-dubnium
    volumes:
      - /dev/shm:/dev/shm
    depends_on:
      - selenium-hub
    ports:
      - "5801:5800"
    environment:
      - HUB_HOST=selenium-hub
      - HUB_PORT=4444
``` 

2. Run below command. Docker compose will download images and run services defined in yaml file.

```
docker-compose up -d
```

Now your selenium grid is up and running. Follow link [localhost:4444](http://localhost:4444) or navigate directly to [grid console](http://localhost:4444/grid/console).

## Setup driver in your project to run tests on selenium grid
**java**
```java
DesiredCapabilities capability = DesiredCapabilities.chrome();
capability.setBrowserName("chrome");
String node = "http://localhost:4444/wd/hub";
driver = new RemoteWebDriver(new URL(node), capability);
```

**c#**
```csharp
DesiredCapabilities capability = DesiredCapabilities.Chrome();
cap.setBrowserName("chrome");
string node = "http://localhost:4444/wd/hub";
IWebDriver driver = new RemoteWebDriver(new Uri(node), capability, TimeSpan.FromSeconds(600));
```

**ruby - capybara**
```ruby
node = 'http://localhost:4444/wd/hub'
capabilities = Selenium::WebDriver::Remote::Capabilities.chrome
Capybara.register_driver :remote_browser do |app|
  Capybara::Selenium::Driver.new(
      app,
      browser: :remote,
      url: node,
      desired_capabilities: capabilities
  )
end
Capybara.default_driver = :remote_browser
Capybara.javascript_driver = :remote_browser
```