# react-native-page-listview

对ListView/FlatList的封装,可以很方便的分页加载网络数据,还支持自定义下拉刷新View和上拉加载更多的View.兼容高版本FlatList和低版本ListVIew.组件会根据你使用的react-native的版本自动选择(高版本使用FlatList,低版本使用ListView)

github地址: https://github.com/geek-prince/react-native-page-listview

npm地址: https://www.npmjs.com/package/react-native-page-listview

#1.0.3->1.1.0改动/新增:
```
    1.增加手动处理数组数据方法,
    2.父组件重新加载数据后手动刷新数据
    3.从网络获取数据,数据为空时的渲染界面,
    4.解决部分手机上界面为空,不显示的问题,(鉴于自定义组件宽高实用性并不大,而且部分手机显示有问题,去除自定义组件宽高,改为自适应)
    5.对放在scrollView中的支持(放在ScrollView中时则不能上拉刷新,下拉加载更多)
    6.加入可选属性allLen,对于分页显示时可以指定数据的总条数
```

## 安装
`npm install react-native-page-listview --save`

## 如何使用

`下面说明中的'组件'指的就是当前这个'react-native-page-listview'组件.`

首先导入组件

`import PageListView from 'react-native-page-listview';`

### 1.不分页,不从网络获取数据(用于本地数组数据的展示)

这时你只需要给组件传递一个数组

`let arr=[你要在ListView上展示的数据数组]`

在render方法中使用该组件

```
<PageListView 
	renderRow={this.renderRow} 
	refresh={this.refresh} 
/>
```

`renderRow`方法中需要你指定每一行数据的展示View,与`ListView/FlatList`的`renderRow/renderItem`方法相同

```
renderRow=(rowData,index)=>{
	return(<View>你的View展示</View>);
}
```

`refresh`方法中指定需要展示数据的数组

```
refresh=(callBack)=>{
	callBack(arr);
}
```

### 2.不分页,从网络获取数据(用于网络数组数据不多,后端接口没有用分页时)

这时与上面使用方法一致,只需要更改一下`refresh`方法

```
refresh=(callBack)=>{
	fetch(url)
        .then((response)=>response.json())
        .then((responseData)=>{
			//根据接口返回结果得到数据数组
			let arr=responseData.result;
            callBack(arr);
        });
}
```

以上这两种方式渲染结果如下(没有下拉刷新和上拉更多):

![(没有分页的渲染效果)](http://github.jikeclub.com/pageListView/s1.gif)

github上加载不出来或显示有问题,请点击这里: http://github.jikeclub.com/pageListView/s1.gif

### 3.从网络获取数据并分页,不自定义上拉刷新,下拉加载更多View(用于数据较多,需要分页请求数据时)

这时你需要给组件一下几个属性`pageLen`,`renderRow`,`refresh`,`loadMore`.

```
<PageListView 
	pageLen={15} 
	renderRow={this.renderRow} 
	refresh={this.refresh} 
	loadMore={this.loadMore} 
/>
```

`pageLen`指定你每次调用后端分页接口可以获得多少条数据.
`renderRow`使用方法和上面相同,渲染每一行的展示.
`refresh`方法会在你组件一开始加载和你下拉刷新时调用,所以你在这个方法中需要将你从后端分页接口的第一页请求返回的数据通过回调传给组件.

```
refresh=(callBack)=>{
	fetch(分页接口url+'?page=1')
        .then((response)=>response.json())
        .then((responseData)=>{
			//根据接口返回结果得到数据数组
			let arr=responseData.result;
            callBack(arr);
        });
}
```

`loadMore`方法会在你下拉加载更多时调用,这时除了`callBack`还会传给你另一个参数`page`表示即将要加载的分页数据是第几页,这时你只需要根据`page`把相应第几页的数组数据通过回调传给组件就行.

```
loadMore=(page,callback)=>{
	fetch(分页接口url+'?page='+page)
		.then((response)=>response.json())
		.then((responseData)=>{
			//根据接口返回结果得到数据数组
			let arr=responseData.result;
            callBack(arr);
		});
};
```

这种情况下显示的渲染效果为:

![(有分页不自定义View的渲染效果)](http://github.jikeclub.com/pageListView/s2.gif)

github上加载不出来或显示有问题,请点击这里: http://github.jikeclub.com/pageListView/s2.gif

### 4.从网络获取数据并分页,并且自定义下拉刷新,上拉加载更多View

渲染下拉刷新View使用`renderRefreshView`,且此时需要给定它的高度`renderRefreshViewH`,渲染加载更多View使用`renderLoadMore`,渲染没有更多数据的View使用`renderNoMore`.

```
<PageListView 
	pageLen={15} 
	renderRow={this.renderRow} 
	refresh={this.refresh} 
	loadMore={this.loadMore} 
	//上面四个属性使用方法同上
	renderRefreshView={this.renderRefreshView}
	renderRefreshViewH={150}
	renderLoadMore={this.renderLoadMore}
	renderNoMore={this.renderNoMore}
/>
```

```
renderRefreshView=()=>{
        return(
            <View style={{}}>//你对渲染下拉刷新View的代码</View>
        );
    };
```

```
renderLoadMore=()=>{
        return(
            <View style={{}}>//你对渲染加载更多View的代码</View>
        );
    };
```

```
renderNoMore=()=>{
        return(
            <View style={{}}>//你对渲染没有更多数据时View的代码</View>
        );
    };
```

这种情况下显示的渲染效果为:

![(有分页自定义View的渲染效果)](http://github.jikeclub.com/pageListView/s3.gif)

github上加载不出来或显示有问题,请点击这里: http://github.jikeclub.com/pageListView/s3.gif

## 拓展

### 下拉刷新进阶

如果你想实现更好看更绚丽的下拉刷新效果,可以像下面这样使用`renderRefreshView`.

`pullState`会根据你下拉的状态给你返回相应的字符串:

* `''` : 没有下拉动作时的状态
* `'pulling'` : 正在下拉并且还没有拉到指定位置时的状态
* `'pullOk'` : 正在下拉并且拉到指定位置时并且没有松手的状态
* `'pullRelease'` : 下拉到指定位置后并且松手后的状态

```
renderRefreshView=(pullState)=>{
        switch (pullState){
            case 'pullOk':
                return <View style={}>
					//下拉刷新,下拉到指定的位置时,你渲染的View
                </View>;
                break;
            case 'pullRelease':
                return <View style={}>
                    //下拉刷新,下拉到指定的位置后,并且你松手后,你渲染的View
                </View>;
                break;
            case '':
            case 'pulling':
            default:
                return <View style={}>
					//下拉刷新,你正在下拉时还没有拉到指定位置时(或者默认情况下),你渲染的View
                </View>;
                break;
        }
    };
```

这种情况下显示的渲染效果为:

![(有分页自定义复杂下拉刷新View的渲染效果)](http://github.jikeclub.com/pageListView/s4.gif)

github上加载不出来或显示有问题,请点击这里: http://github.jikeclub.com/pageListView/s4.gif

### 对数据数组进行处理

有时候我们不一定会直接渲染从后端取回来的数据,需要对数据进行一些处理,这时可以在组件中加入`dealWithDataArrCallBack`属性来对数组数据进行一些处理.下面是把从后端取回来的数组进行顺序的颠倒.

```
<PageListView 
	//其他的属性...
	dealWithDataArrCallBack={(arr)=>{return arr.reverse()}}
/>
```

上面对数组的操作只会在分页请求数据的"上拉刷新"和"下拉加载更多"时触发.有的时候我们可能要在某个点击事件等操作之后对数据数组进行操作,修改.这个时候就可以通过主动调用组件的`changeDataArr`方法来实现.这时需要先对组件进行ref引用

```
<PageListView 
	//其他的属性...
	refs={(r)=>{!this.PL&&(this.PL=r)}}
/>
```

然后在执行某个操作时需要修改数组数据时通过主动调用`changeDataArr`方法来实现

```
this.PL1.changeDataArr((arr)=>{return arr.reverse()});
```

### 手动刷新数据

这种情况通常用于组件显示数据分类中的某一类的情况,然后父组件中更改了筛选的分类,父组件中获得数组数据需要手动把数据传给组件,并刷新组件,这个时候可以用到`manualRefresh`这个方法,这个方法也需要先像上面一样获取到组件的ref引用.然后在父组件获得数据数组`res`后

```
this.PL.manualRefresh(res);
```
手动刷新组件

### 没有一条数据时的渲染

如果获取到的数据数组为空,可以通过`renderEmpty`方法来渲染这种情况下要显示的View

```
<PageListView 
	//其他的属性...
	renderEmpty={this.renderEmpty}
/>
renderEmpty=()=>{return(<View style={[]}>你的View渲染代码</View>);}
```

### 小功能

另外,`FlatList`中有个属性为`ItemSeparatorComponent`是设置每一行View之间分割的View的,自己觉得不错就写到组件里了,兼容`ListView`.

如果需要把组件放到scrollView中时加入`inScrollView={true}`的属性,但此时便不能使用上拉刷新,下拉加载更多.

## 属性一览表:

| props | 作用 |默认值|
| :-------------: |:-------------:|:-------------:|
|renderRow|处理"渲染FlatList/ListView的每一行"的方法|null|
|refresh|处理"下拉刷新"或"一开始加载数据"的方法|null|
|loadMore|处理"加载更多"的方法|null|
|pageLen|每个分页的数据数|0|
|allLen|总的数据条数|0|
|dealWithDataArrCallBack|如果需要在用当前后端返回的数组数据进行处理的话,传入回调函数|null|
|renderLoadMore|还有数据可以从后端取得时候渲染底部View的方法|null|
|renderNoMore|没有数据(数据已经从后端全部加载完)是渲染底部View的方法|null|
|renderRefreshView|渲染下拉刷新的View样式|null|
|renderRefreshViewH|渲染下拉刷新的View样式的高度|60|
|renderEmpty|如果网络获取数据为空时的渲染界面|null|
|ItemSeparatorComponent|渲染每行View之间的分割线View|null|
|inScrollView|当前组件是否是放在scrollView中(放在ScrollView中时则不能上拉刷新,下拉加载更多)|false|
|FlatList/ListView自身的属性|是基于FlatList/ListView,所以可以直接使用他们自身的属性|

`注意:如果屏幕下方有绝对定位的View时,这时组件自适应宽高,下面的一部分内容会被遮挡,这时一个很好的解决方法是在组件下方渲染一个与绝对定位等高的透明View来解决(<View style={{height:你绝对定位View的高度,backgroundColor:'#0000'}}/>).`

如果大家觉得我的组件好用的话,帮到你的话,欢迎大家Star,Fork,如果有什么问题的话也可以在github中想我提出,谢谢大家的支持.

