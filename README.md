# generate-it-typescript-client-to-server

Generate-it typescript set of tpls for browser to client api client.

Uses Axios by default but you can drop in another http service file to replace axios:

Default lib:
```
  "scripts": {
    "generate:client": "generate-it ../ms_item_d/build/ms-item-d_1.0.0.yml -t https://github.com/acrontum/generate-it-typescript-client-to-server.git"
```
The [default http lib](https://github.com/acrontum/generate-it-typescript-client-to-server/blob/master/lib/HttpService.ts.njk) uses axios and simply calls the API and returns the promise.

Overriding the default http lib:
```
  "scripts": {
    "generate:client": "generate-it ../ms_item_d/build/ms-item-d_1.0.0.yml -t https://github.com/acrontum/generate-it-typescript-client-to-server.git -$ httpServiceImport=@/services/HttpService"
```
The default lib could very likely be too simple for your application, or you maybe you don't want to use axios. Just add -$ httpServiceImport=<the project import> to replace the http service with your own version and then use whichever options you need, and override exceptions as you need. 
