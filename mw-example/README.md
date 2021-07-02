### To start
$ docker-compose up -d
Visit http://cerebro.localhost
or curl -H Host:cerebro.localhost http://127.0.0.1

### To scale and load-balance
$ docker-compose up -d --scale cerebro=3

### Note
- middleware names should be unique
- Path-prefix must be stripped-off
