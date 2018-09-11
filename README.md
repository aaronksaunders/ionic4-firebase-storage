# ionic4-firebase-storage


### Magic of converting image from camera to blob for upload

camera code is directly copied from the [ionic-native documentation](https://ionicframework.com/docs/native/)

```javascript
const options: CameraOptions = {
  quality: 80,
  destinationType: this.camera.DestinationType.FILE_URI,
  encodingType: this.camera.EncodingType.JPEG,
  mediaType: this.camera.MediaType.PICTURE
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

   ionic (Ionic CLI)          : 4.1.1 (/Users/aaronsaunders/.nvm/versions/node/v9.3.0/lib/node_modules/ionic)
   Ionic Framework            : @ionic/angular 4.0.0-beta.7
   @angular-devkit/core       : 0.7.5
   @angular-devkit/schematics : 0.7.5
   @angular/cli               : 6.1.5
   @ionic/ng-toolkit          : 1.0.8
   @ionic/schematics-angular  : 1.0.6

Cordova:

   cordova (Cordova CLI) : 8.0.0
   Cordova Platforms     : ios 4.5.5
   Cordova Plugins       : cordova-plugin-ionic-keyboard 2.1.2, cordova-plugin-ionic-webview 2.1.3, (and 7 other plugins)

System:

   Android SDK Tools : 26.1.1 (/Users/aaronsaunders/Library/Android/sdk)
   ios-deploy        : 1.9.2
   NodeJS            : v9.3.0 (/Users/aaronsaunders/.nvm/versions/node/v9.3.0/bin/node)
   npm               : 6.4.0
   OS                : macOS High Sierra
   Xcode             : Xcode 9.4.1 Build version 9F2000
```
