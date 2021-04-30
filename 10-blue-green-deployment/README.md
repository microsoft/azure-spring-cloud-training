# 10 - Blue/Green deployment

__This guide is part of the [Azure Spring Cloud training](../README.md)__

The blue-green deployment pattern allows you to test latest application changes on production infrastructure, but without exposing the changes to consumers until your testing is complete. In this section, we'll perform a blue-green deployment with Azure CLI. Although we will go through the deployment steps manually, the Azure CLI commands we'll use can be automated in a CI/CD pipeline.

---

We are going to deploy a new release of the "weather-service" microservice that was developed in [07 - Build a Spring Boot microservice using MySQL](../07-build-a-spring-boot-microservice-using-mysql/README.md).

The microservice that we develop in this guide is [available here](weather-service/), which is a slightly modified version of the service that was developed earlier.

## Modify the current application

In the "weather-service" application, commit your current code and switch to a new branch where you will do your changes.

Open the `WeatherController` class and modify its `getWeatherForCity()` method so it always returns sunny weather (this will be an easy-to-spot graphical view of our modifications in the code):

```java
package com.example.demo;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

@Controller
@RequestMapping(path="/weather")
public class WeatherController {

    private final WeatherRepository weatherRepository;

    public WeatherController(WeatherRepository weatherRepository) {
        this.weatherRepository = weatherRepository;
    }

    @GetMapping("/city")
    public @ResponseBody Weather getWeatherForCity(@RequestParam("name") String cityName) {
        return weatherRepository.findById(cityName).map(weather -> {
            weather.setDescription("It's always sunny on Azure Spring Cloud");
            weather.setIcon("weather-sunny");
            return weather;
        }).get();
    }
}
```

## Deploy the new application to a new "green" deployment

Build a new version of the application and deploy it to a new `deployment` called `green`:

```bash
cd weather-service
./mvnw clean package -DskipTests
az spring-cloud app deployment create --name green --app weather-service --jar-path target/demo-0.0.1-SNAPSHOT.jar
cd ..
```

Once the application is deployed, if you go to [https://spring-training.azureedge.net/](https://spring-training.azureedge.net/) you will still have the same data, as the new version of the microservice is now in a staging area and not in production yet.

Navigate to the Azure Spring Cloud instance in [the Azure portal](https://portal.azure.com/?WT.mc_id=azurespringcloud-github-judubois) and click on "Deployments" (under Settings). You should now see the "green" deployment in the "Staging Deployment" column:

![Deployment Pane](media/02-deployment-pane.png)

You can test the `green` deployment by invoking the same URL as in section 7, but replacing the deployment name `default` with `green`:

```bash
curl https://***.test.azuremicroservices.io/weather-service/green/weather/city?name=Paris%2C%20France
```

And you should see the result of the recent modification:

```json
{"city":"Paris, France","description":"It's always sunny on Azure Spring Cloud","icon":"weather-sunny"}
```

Note: we're not testing the green deployment through the `gateway` application. The purpose of a green deployment is to test changes to a microservice before routing production traffic to it. Therefore, if you access `weather-service` through the public Gateway URL, as you did in section 8, you will be routed to the original version of the service.

To put this `green` deployment into production, you can use the command line:

```bash
az spring-cloud app set-deployment -n weather-service --deployment green
```

Another solution is to use [the Azure portal](https://portal.azure.com/?WT.mc_id=azurespringcloud-github-judubois):

- Find your Azure Spring Cloud instance
- Click on the "deployment" menu
- Select the `weather-service` application and click on "Set deployment"

> If you want to reuse a deployment name, you need first to delete the previous deployment under that name:
>
> ```bash
> az spring-cloud app deployment delete --name green --app weather-service
> ```

Once you have swapped deployments and see that `green` is active, you need to wait a few seconds for the Spring Cloud Service Registry to synchronize and use this new version from the `gateway` application. You will then be able to see the new modified data:

![Green deployment](media/01-green-deployment.png)


## Conclusion

Here is the final script to change the application, deploy it to a "green" deployment and then promote it to the production deployment.

```bash
cd weather-service
cat > src/main/java/com/example/demo/WeatherController.java << EOF
package com.example.demo;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

@Controller
@RequestMapping(path="/weather")
public class WeatherController {

    private final WeatherRepository weatherRepository;

    public WeatherController(WeatherRepository weatherRepository) {
        this.weatherRepository = weatherRepository;
    }

    @GetMapping("/city")
    public @ResponseBody Weather getWeatherForCity(@RequestParam("name") String cityName) {
        return weatherRepository.findById(cityName).map(weather -> {
            weather.setDescription("It's always sunny on Azure Spring Cloud");
            weather.setIcon("weather-sunny");
            return weather;
        }).get();
    }
}
EOF
./mvnw clean package -DskipTests -Pcloud
az spring-cloud app deployment create --name green --app weather-service --jar-path target/demo-0.0.1-SNAPSHOT.jar
az spring-cloud app set-deployment -n weather-service --deployment green
cd ..
```
---

⬅️ Previous guide: [09 - Putting it all together, a complete microservice stack](../09-putting-it-all-together-a-complete-microservice-stack/README.md)

➡️ Next guide: [11 - Configure CI/CD](../11-configure-ci-cd/README.md)
