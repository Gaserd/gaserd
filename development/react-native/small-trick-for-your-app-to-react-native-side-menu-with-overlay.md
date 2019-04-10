# Small trick for your app to React Native - Side Menu with Overlay

Hi, everyone!

I like React Native and i writed couple of articles:

* [Analytics for React Native](https://dev.to/gaserd/how-make-analytics-for-your-app-in-react-native--35kf)
* [Mobile life](https://dev.to/gaserd/mobile-development-in-real-life-5gjd)

Yeah, this is most popular question, how make overlay view from side menu.

SideMenu.js

```text
import React, { Component } from 'react';
import {
    View, 
    Text 
} from 'react-native';

import MenuList from './containers/MenuList'
import MenuOverlay from './containers/MenuOverlay'

import styles from './style/SideMenuStyle'

export default class SideMenu extends Component {
    render() {        
        let { 
            navigation,
            onToggleMenu 
        } = this.props

        return (
            <View style={styles.container}>
                <MenuOverlay 
                    onToggleMenu={onToggleMenu}
                    navigation={navigation}
                />
                <View style={styles.menu}>
                    <MenuList 
                        onToggleMenu={onToggleMenu}
                        navigation={navigation} 
                    />
                </View>
            </View>
        );
    }
}
```

./style/SideMenuStyle

```text
import { StyleSheet, Dimensions } from 'react-native';

let width = Dimensions.get('window').width
let height = Dimensions.get('window').height

export default styles = StyleSheet.create({
    container : {
        flex: 1,
        position : 'absolute',
        left: 0,
        top: 0,
        width : width, 
        height : height,
        paddingTop : 10,
        paddingLeft : 10,
        paddingRight : 10,
        paddingBottom : 10
    },
    menu: {
        flex: 1,
        backgroundColor: '#FFF',
        position : 'absolute',
        left: 0,
        top: 0,
        width : width * 0.8, 
        height : height,
        paddingTop : 10,
        paddingLeft : 10,
        paddingRight : 10,
        paddingBottom : 10
    },
    menuItem : {
        paddingTop : 10
    }
});

```

MenuOverlay.js

```text
import React, { Component } from 'react';
import {
    TouchableHighlight,
    Text
} from 'react-native';

import styles from '../style/MenuOverlayStyle'

export default class MenuOverlay extends Component {

    render() {

        return (
            <TouchableHighlight 
                onPress={() => {
                    this.props.onToggleMenu()
                }}
                style={styles.overlay}>
                <Text></Text>
            </TouchableHighlight>
        );
    }
}
```

../style/MenuOverlayStyle

```text
import { StyleSheet, Dimensions } from 'react-native';

let width = Dimensions.get('window').width
let height = Dimensions.get('window').height

export default styles = StyleSheet.create({
    overlay: {
        backgroundColor: 'rgba(52, 52, 52, 0.8)',
        position : 'absolute',
        left: 0,
        top: 0,
        width : width, 
        height : height,
        paddingTop : 10,
        paddingLeft : 10,
        paddingRight : 10,
        paddingBottom : 10
    }
});
```

How it work?

[![chick](https://res.cloudinary.com/practicaldev/image/fetch/s--22TVw2fA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://im.ezgif.com/tmp/ezgif-1-d597dfb718.gif)](https://res.cloudinary.com/practicaldev/image/fetch/s--22TVw2fA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://im.ezgif.com/tmp/ezgif-1-d597dfb718.gif)

