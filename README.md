# ios_facebook_login
React Native iOS Facebook login example.

**STEP 1.** 請參考 [FB Login 官方文件](https://developers.facebook.com/docs/react-native/login/)。

**STEP 2.** [在FB上建立應用程式](https://developers.facebook.com/docs/ios/getting-started/)，以取得相關資訊來填入 xcode 專案。

**STEP 3.** facebook-ios-sdk v7之後版本，別忘了在 Xcode 專案[新增 File.Swift](https://github.com/facebook/react-native-fbsdk#troubleshooting)，否則編譯時會出現下列 Error：
```
Undefined symbol: __swift_FORCE_LOAD_$_ swiftUniformTypeIdentifiers
Undefined symbol: __swift_FORCE_LOAD_$_ swiftCoreMIDI
Undefined symbol: __swift_FORCE_LOAD_$_ swiftWebKit
```

**STEP 4.** 最後，使用 [Graph API](https://developers.facebook.com/docs/react-native/graph-api) 來獲得登入者的資訊。

** Example Code **
```
# FBLoginButton.js

import React, { Component } from 'react';
import { View } from 'react-native';
import { LoginButton } from 'react-native-fbsdk';

const FBSDK = require('react-native-fbsdk');
const {
  GraphRequest,
  GraphRequestManager,
  AccessToken,
} = FBSDK;


export default class FBLoginButton extends Component {

  //Create response callback.
  _responseInfoCallback = (error, result) => {
    if (error) {
      alert('Error fetching data: ' + error.toString());
    } else {
      alert('Result Name: ' + result.name);
      console.log(result);
    }
  }

  render() {
    return (
      <View>
        <LoginButton
          publishPermissions={["email"]}
          onLoginFinished={
            (error, result) => {
              if (error) {
                alert("Login failed with error: " + error.message);
              } else if (result.isCancelled) {
                alert("Login was cancelled");
              } else {

                AccessToken.getCurrentAccessToken().then(
                  (data) => {
                    const infoRequest = new GraphRequest(
                      '/me?fields=name,picture',
                      null,
                      this._responseInfoCallback
                    );
                    // Start the graph request.
                    new GraphRequestManager().addRequest(infoRequest).start();
                  }
                )
              }
            }
          }
          onLogoutFinished={() => alert("User logged out")}/>
      </View>
    );
  }
};

module.exports = FBLoginButton;
```

```
# App.js

import React from 'react';
import {
  StyleSheet,
  View,
  Text,
} from 'react-native';

var FBLoginButton = require('./FBLoginButton');

const App: () => React$Node = () => {
  return (
    <View style={styles.container}>
      <Text style={styles.label}>
        Welcome to the Facebook SDK for RN!
      </Text>
      <FBLoginButton />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'column',
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  label: {
    fontSize: 16,
    fontWeight: 'normal',
    marginBottom: 48,
  },
});

export default App;
```

