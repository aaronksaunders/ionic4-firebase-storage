# ionic4-firebase-storage

#### See additional content on youtube - https://www.youtube.com/channel/UCMCcqbJpyL3LAv3PJeYz2bg
> Updated October 30th 2019

### Magic of converting image from camera to blob for upload

camera code is directly copied from the [ionic-native documentation](https://ionicframework.com/docs/native/)

```javascript
const options: CameraOptions = {
  quality: 80,
  quality: 80,
  destinationType: this.camera.DestinationType.FILE_URI,
  encodingType: this.camera.EncodingType.JPEG,
  mediaType: this.camera.MediaType.ALLMEDIA,
  sourceType : this.camera.PictureSourceType.PHOTOLIBRARY
};

let cameraInfo = await this.camera.getPicture(options);
```

be sure to include the [cordova-file plugin](https://ionicframework.com/docs/native/file/) and the [cordova-camera plugin](https://ionicframework.com/docs/native/camera/)
```javascript

  // FILE STUFF
  makeFileIntoBlob(_imagePath) {
    // INSTALL PLUGIN - cordova plugin add cordova-plugin-file
    return new Promise((resolve, reject) => {
      let fileName = "";
      this.file
        .resolveLocalFilesystemUrl(_imagePath)
        .then(fileEntry => {
          let { name, nativeURL } = fileEntry;

          // get the path..
          let path = nativeURL.substring(0, nativeURL.lastIndexOf("/"));

          fileName = name;

          // we are provided the name, so now read the file into a buffer
          return this.file.readAsArrayBuffer(path, name);
        })
        .then(buffer => {
          // get the buffer and make a blob to be saved
          let imgBlob = new Blob([buffer], {
            type: "image/jpeg"
          });
          
          // pass back blob and the name of the file for saving
          // into fire base
          resolve({
            fileName,
            imgBlob
          });
        })
        .catch(e => reject(e));
    });
  }
```
  
### Things to notice in the code

Use the versions of the @ionic/native modules

```json
  "@ionic-native/camera": "^5.16.0",
  "@ionic-native/core": "^5.0.0",
  "@ionic-native/file": "^5.16.0",
  "@ionic-native/splash-screen": "^5.0.0",
  "@ionic-native/status-bar": "^5.0.0",
```

```javascript
// notice the path for the import ends in ngx
import { Camera, CameraOptions } from "@ionic-native/camera/ngx";
import { File } from "@ionic-native/file/ngx";
```
you have to do the same when you import the module in app.module.ts

```javascript
import { Camera } from '@ionic-native/camera/ngx';
import { File } from '@ionic-native/file/ngx';

@NgModule({
  declarations: [AppComponent],
  entryComponents: [],
  imports: [BrowserModule, IonicModule.forRoot(), AppRoutingModule],
  providers: [
    File,
    Camera,
    StatusBar,
    SplashScreen,
    { provide: RouteReuseStrategy, useClass: IonicRouteStrategy }
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

```console
Ionic:

   Ionic CLI                     : 5.2.7 (/Users/aaronksaunders/.nvm/versions/node/v10.15.1/lib/node_modules/ionic)
   Ionic Framework               : @ionic/angular 4.11.3
   @angular-devkit/build-angular : 0.801.3
   @angular-devkit/schematics    : 8.1.3
   @angular/cli                  : 8.1.3
   @ionic/angular-toolkit        : 2.0.0

Cordova:

   Cordova CLI       : 9.0.0 (cordova-lib@9.0.1)
   Cordova Platforms : ios 5.0.1
   Cordova Plugins   : cordova-plugin-ionic-keyboard 2.2.0, cordova-plugin-ionic-webview 2.5.3, (and 7 other plugins)

Utility:

   cordova-res : not installed
   native-run  : 0.2.9 

System:

   Android SDK Tools : 26.1.1 (/Users/aaronksaunders/Library/Android/sdk)
   ios-deploy        : 1.9.4
   ios-sim           : 8.0.2
   NodeJS            : v10.15.1 (/Users/aaronksaunders/.nvm/versions/node/v10.15.1/bin/node)
   npm               : 6.11.2
   OS                : macOS Mojave
   Xcode             : Xcode 11.1 Build version 11A1027

Aarons-iMac:ionic4-firebase-storage aaronksaunders$ 
```
