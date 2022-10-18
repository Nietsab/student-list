## Synthèse TP Docker - Bastien, Grégory, Erwann

### Build and test 

- Création du Fichier Dockerfile dans le dossier de l'api 
``` FROM python:2.7-stretch LABEL maintainer='Nous'
RUN apt-get install git -y
ADD . /
RUN apt-get update -y && \
apt-get install python-dev python3-dev libsasl2-dev python-dev libldap2-dev libssl-dev -y && \
pip install flask==1.1.2 flask_httpauth==4.1.0 flask_simpleldap python-dotenv==0.14.0
VOLUME /data
EXPOSE 5000
CMD [ "python", "./student_age.py" ]
```

- On build le container 
  ``` docker build . ```
 
- Ensuite on lance le container
  ``` docker run -v $PWD/student_age.json:/data/student_age.json -d --name student-list -p 8081:5000 student-list ``` 

- Ensuite on vérifie qu'on a accès au données du fichier JSON 

    ``` curl -u toto:python -X GET http://127.0.0.1:8081/pozos/api/v1.0/get_student_ages ```


### Infrastructure As Code

- On modifie le docker-compose.yml 
  ```services: 
  website:
  container_name: 'website'
  image: 'php:apache'
  environment:
   - USERNAME=username
   - PASSWORD=password
  volumes:
   - './website:/var/www/html'
  depends_on:
   - api
  ports:
   - '8081:5000'
  networks:
   - network
  api:
   container_name: 'api'
   image: 'student-list'
   volumes:
    - ./simple_api/student_age.json:/data/student_age.json
   ports:
    - '8082:5000'
   networks:
    - network

   networks:
    network:
