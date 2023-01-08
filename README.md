# Personal Website of Joachim Schuster

To run locally using Docker

Initial container create and startup
```
docker compose up -d
```

Stop
```
docker stop jsblog
```

Start
```
docker start jsblog
```

On configuration changes (while started)
```
docker exec -it jsblog bundle exec jekyll build
```

To refresh site generation (while started)
```
docker exec -it jsblog bundle exec jekyll clean
```

For changes on the gems
```
docker exec -it jsblog bundle install
```
