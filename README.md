

# Ahmed Osama style generator (UI)

>

## Build Setup


# install dependencies
```bash
$ npm install
```
# .env file

create a .env file with the location of the flask server which
contains just the `AXIOS_BASE_URL`

Fx
```bash
    # .env
    AXIOS_BASE_URL = "http://127.0.0.1:5000/"
```
# serve with hot reload at localhost:3000
```bash
$ npm run dev
```
# build for production and launch server
```bash
$ npm run build
$ npm run start
```
# generate static project (if you want to serve static fx nginx (recomended))
```bash
$ npm run generate
```