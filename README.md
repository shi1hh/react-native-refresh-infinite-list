# React Native RefreshableList
A awesome pull-down-refresh and pull-up-loadmore listview

This is an updated version of this repo with ES6 Class component: https://github.com/remobile/react-native-refresh-infinite-listview

## Todo
* Migrate to FlatList component

## Installation
```sh
npm install react-native-refresh-infinite-list  --save
```

or

```sh
yarn add react-native-refresh-infinite-list
```

## Usage

### Example
```js
'use strict';

import React from 'react';
import  {
    View,
    Text,
    StyleSheet,
    ListView
} from 'react-native';
import TimerMixin from 'react-timer-mixin';
import RefreshInfiniteList from 'react-native-refresh-infinite-list';

var ds = new ListView.DataSource({rowHasChanged: (r1, r2) => r1 !== r2});
export default class extends React.Component {
    static mixins: [TimerMixin];
    static data: {index: 0, maxIndex:20, list:[]};

    constructor(props) {
        super(props);
        this.getData(true);
        this.state = {
            dataSource: ds.cloneWithRows(this.data.list)
        }
        this.onRefresh = this.onRefresh.bind(this);
        this.onInfinite = this.onInfinite.bind(this);
        this.loadedAllData = this.loadedAllData.bind(this);
        this.renderRow = this.renderRow.bind(this);
        this.renderSeparator = this.renderSeparator.bind(this);
    }

    getData(init) {
        var total = 5;
        if (init) {
            this.data.index = 0;
            this.data.list = [];
            total = Math.floor(Math.random()*5);
        }
        for (var i=0; i<total; i++) {
            this.data.list[this.data.index] = "Row" + (this.data.index+1);
            this.data.index++;
        }
    }

    onRefresh() {
        this.getData(true);
        this.setTimeout(()=>{
            this.list.hideHeader();
            this.setState({dataSource: ds.cloneWithRows(this.data.list)});
        }, 1000);
    }

    onInfinite() {
        this.getData();
        this.setTimeout(()=>{
            this.list.hideFooter();
            this.setState({dataSource: ds.cloneWithRows(this.data.list)});
        }, 1000);
    }

    loadedAllData() {
        return this.data.index >= this.data.maxIndex||this.data.index===0;
    }

    renderRow(text) {
        return (
            <View style={styles.row}>
                <Text >
                    {text}
                </Text>
            </View>
        )
    }

    renderSeparator(sectionID, rowID) {
        return (
            <View style={styles.separator} key={sectionID+rowID}/>
        );
    }

    render() {
        return (
            <View style={{flex:1}}>
                <View style={{height:20}} />
                <RefreshInfiniteListView
                    ref = {(list) => {this.list= list}}
                    dataSource={this.state.dataSource}
                    renderRow={this.renderRow}
                    renderSeparator={this.renderSeparator}
                    loadedAllData={this.loadedAllData}
                    initialListSize={30}
                    scrollEventThrottle={10}
                    style={{backgroundColor:'transparent'/*,top:100, left:10, width:200, height:300, position:'absolute'*/}}
                    onRefresh = {this.onRefresh}
                    onInfinite = {this.onInfinite}
                    >
                </RefreshInfiniteListView>
            </View>
        )
    }
};

var styles = StyleSheet.create({
    row: {
        height:60,
        flex: 1,
        flexDirection: 'row',
        alignItems: 'center',
        justifyContent: 'center'
    },
    list: {
        alignSelf:'stretch'
    },
    separator: {
        height: 1,
        backgroundColor: '#CCC'
    },
});
```

## Screencasts

![refresh](https://github.com/remobile/react-native-refresh-infinite-listview/blob/master/screencasts/refresh.gif)

#### Props
- `footerHeight:number`
    if you need use infinite with custom, must set footerHeight
- `pullDistance:number`
    set pull distance, default is 50
- `renderEmptyRow:func`
```js
            renderEmptyRow: () => {
                return (
                    <View style={{height:Dimensions.get('window').height, justifyContent:'center',alignItems:'center'}}>
                        <Text style={{fontSize:40, fontWeight:'800', color:'red'}}>
                            have no data
                        </Text>
                    </View>
                )
            },
```
- `renderHeaderRefreshIdle:func`
```js
            renderHeaderRefreshIdle: () => {return (
                <View style={{flex:1, height:DEFAULT_HF_HEIGHT, justifyContent:'center', alignItems:'center'}}>
                    <Text style={styles.text}>
                        pull down refresh...
                    </Text>
                    <Image
                        source={require('./pull_arrow.png')}
                        resizeMode={Image.resizeMode.stretch}
                        style={styles.image}
                        />
                </View>
            )},
```
- `renderHeaderWillRefresh:func`
```js
            renderHeaderWillRefresh: () => {return (
                <View style={{height:DEFAULT_HF_HEIGHT, justifyContent:'center', alignItems:'center'}}>
                    <Text style={styles.text}>
                        release to refresh...
                    </Text>
                    <Image
                        source={require('./pull_arrow.png')}
                        resizeMode={Image.resizeMode.stretch}
                        style={[styles.image, styles.imageRotate]}
                        />
                </View>
            )},
```
- `renderHeaderRefreshing:func`
```js
            renderHeaderRefreshing: () => {return (
                <View style={{height:DEFAULT_HF_HEIGHT, justifyContent:'center', alignItems:'center'}}>
                    <Text style={styles.text}>
                        refreshing...
                    </Text>

                    <ActivityIndicatorIOS
                        size='small'
                        animating={true}/>
                </View>
            )},
```
- `renderHeaderRefreshing:func`
```js
            renderFooterInifiteIdle: () => {return (
                <View style={{height:DEFAULT_HF_HEIGHT, justifyContent:'center', alignItems:'center'}}>
                    <Image
                        source={require('./pull_arrow.png')}
                        resizeMode={Image.resizeMode.stretch}
                        style={[styles.image, styles.imageRotate]}
                        />
                    <Text style={styles.text}>
                        pull up to load more...
                    </Text>
                </View>
            )},
```
- `renderFooterWillInifite:func`
```js
            renderFooterWillInifite: () => {return (
                <View style={{height:DEFAULT_HF_HEIGHT, justifyContent:'center', alignItems:'center'}}>
                    <Image
                        source={require('./pull_arrow.png')}
                        resizeMode={Image.resizeMode.stretch}
                        style={styles.image}
                        />
                    <Text style={styles.text}>
                        release to load more...
                    </Text>
                </View>
            )},
```
- `renderFooterInifiting:func`
```js
            renderFooterInifiting: () => {return (
                <View style={{height:DEFAULT_HF_HEIGHT, justifyContent:'center', alignItems:'center'}}>
                    <ActivityIndicatorIOS
                        size='small'
                        animating={true}/>
                    <Text style={styles.text}>
                        loading...
                    </Text>
                </View>
            )},
```
- `renderFooterInifiteLoadedAll:func`
```js
            renderFooterInifiteLoadedAll: () => { return (
                <View style={{height:DEFAULT_HF_HEIGHT, justifyContent:'center', alignItems:'center'}}>
                    <Text style={styles.text}>
                        have loaded all data
                    </Text>
                </View>
            )},
```
- `onRefresh:func`
```js
            onRefresh: () => {
                console.log("onRefresh");
            },
```
- `onInfinite':func`
```js
            onInfinite: () => {
                console.log("onInfinite");
            }
```
- `loadedAllData':func`
```js
            loadedAllData: () => {
                return false;
            }
```