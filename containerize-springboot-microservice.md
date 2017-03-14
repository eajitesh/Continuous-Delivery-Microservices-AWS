# Containerize Springboot Microservice

Check out this sample project, [Springboot Microservice](https://github.com/eajitesh/Spring-Boot-Web-App-Template) which represents REST endpoints written using @RestController annotation.

Following code represents the Dockerfile which is used to build the docker image for this microservice.

```
FROM frolvlad/alpine-oraclejdk8:slim
VOLUME /tmp
ADD target/demo-0.0.1-SNAPSHOT.jar app.jar
RUN sh -c 'touch /app.jar'
ENV JAVA_OPTS=""
ENTRYPOINT [ "sh", "-c", "java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app.jar" ]
```

Following represents a REST endpoint. Pay attention to usage of @RestController annotation.

```
package com.example;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class DoctorSearchController {
	
	@Autowired
	DoctorService docService;
	
	@RequestMapping(value="/doctors", method=RequestMethod.GET, produces="application/json")
	public DoctorList searchDoctor(@RequestParam(value="location", required=false) String location,
			@RequestParam(value="speciality", required=false) String speciality) {
		DoctorList docList = docService.find(location, speciality);
		return docList;
	}
}
```
