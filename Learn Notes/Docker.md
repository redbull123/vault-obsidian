
# Introduction to Docker Caching
	Revisar siempre la cache, ya que puede afectar a un docker.

# Cambiar usuarios y directorio de trabajo

	Set the WORKDIR (no bloquea rutas absolutas)
		WORKDIR /home/repl/projects/pipeline
			RUN ./start.sh
		For Default, it is used root user, you can change of user with:
		USER repl   ---> Changes the user to repl
# Variable en los archivos Docker
		Create variables in a Dockerfile
			ARG <var_name>=<var_value>
			ARG path=/home/repl
			COPY  /local/path $path
		Varibles with ENV, it exits still after the images is created.
			ENV DB_USER=pipeine_user
			docker run --env VAR:value image_name
---
# Intermediate Docker

## Temporary containers

```
docker run --rm

docker ps -a ##used to determinate name, id, status, 
```

