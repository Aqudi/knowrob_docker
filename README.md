# Knowrob docker

## Pull and Up

```
docker-compose pull
docker-compose up -d
```

## Build and Push docker images

1. Build apt-cacher-ng container
    ```
    docker-compose build apt-cacher-ng
    ```

2. Build knowrob container
   ```
   docker-compose up -d apt-cacher-ng
   docker-compose build knowrob
   ```

3. Docker login with github token
   
   How to create a personal access token [link](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)

   ```
   cat token.txt | docker login https://ghcr.io -u <UserName> --password-stdin
   ```

4. Push images to github container registry
   ```
   docker-compose push
   ```