# generate-it-typescript-client-to-server

Generate-it typescript set of tpls for browser to client api client.

Uses Axios by default but you can drop in another http service file to replace axios:

## Default lib:
```
  "scripts": {
    "generate:client": "generate-it ../ms_item_d/build/ms-item-d_1.0.0.yml -t https://github.com/acrontum/generate-it-typescript-client-to-server.git"
```
The [default http lib](https://github.com/acrontum/generate-it-typescript-client-to-server/blob/master/lib/HttpService.ts.njk) uses axios and simply calls the API and returns the promise. This default http class will not get overwritten so making changes to this file is safe. If your app consumes many apis then you will likely want to use a shared lib opposed to repeating this one each time... see below.

## Overriding the default http lib:
```
  "scripts": {
    "generate:client": "generate-it ../ms_item_d/build/ms-item-d_1.0.0.yml -t https://github.com/acrontum/generate-it-typescript-client-to-server.git -$ httpServiceImport=@/services/HttpService"
```
The default lib could very likely be too simple for your application, or you maybe you don't want to use axios. Or, which is often the case, your frontend talks to many APIs so managing the http lib from a single files is much more convenient and reduces bloat.

Just add -$ httpServiceImport=<the project import> to replace the http service with your own version and then use whichever options you need, and override exceptions as you need. 

## Overriding the base path
By default the base path is '/'. This will likely be something else if you are using aws ingress routing for example.

Override the base path example to the name of the microservice:
```
generate-it ../ms_item_d/build/ms-item-d_1.0.0.yml -t https://github.com/acrontum/generate-it-typescript-client-to-server.git -$ httpServiceImport=@/services/HttpService  -$ basePath=/ms-authentication/
```

## Example multiclient generation script
```javascript
// relative paths to the script caller (from and to)
const config = [
  {
    from: '../../backend/ms_authentication_d/build/ms-authentication-d_1.0.1.yml',
    to: 'src/api/ms-authentication'
  },
  {
    from: '../../backend/ms_item_d/build/ms-item-d_1.0.0.yml',
    to: 'src/api/ms-item'
  },
]
const generateIt = require('generate-it/build/generateIt').default
const generate = (configArray) => {
  if (configArray.length === 0) {
    console.log('Completed the generate of all apis.')
  } else {
    const item = configArray.shift()
    generateIt({
      dontRunComparisonTool: false,
      dontUpdateTplCache: false,
      mockServer: false,
      segmentsCount: 1,
      swaggerFilePath: item.from,
      targetDir: item.to,
      template: 'https://github.com/acrontum/generate-it-typescript-client-to-server.git',
      variables: {
        httpServiceImport: '@/services/HttpService',
        basePath: '/' + item.to.split('/') + '/'
      }
    })
      .then(() => {
        console.log('API generated: ' + item.to)
        generate(configArray)
      })
      .catch((e) => {
        console.log('API generation error: ', e)
      })
  }
}
generate(config)
```
