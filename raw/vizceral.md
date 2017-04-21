[TOC]
# Getting Started

[React](https://github.com/Netflix/vizceral-react) for react
[Web Components](https://github.com/Netflix/vizceral-component) for web
[Example](https://github.com/Netflix/vizceral-example) demo

# Configuration 配置

## Contents 内容
### Styling 样式


底层基于three.js， 其都是基于js去定义样式，所以此处没有通过css去定义样式

```js
import Vizceral from 'vizceral';
const vizceral = new Vizceral();
vizceral.updateStyles({
  colorText: 'rgb(214, 214, 214)',
  colorTextDisabled: 'rgb(129, 129, 129)',
  colorTraffic: {
    normal: 'rgb(186, 213, 237)',
    normalDonut: 'rgb(91, 91, 91)',
    warning: 'rgb(268, 185, 73)',
    danger: 'rgb(184, 36, 36)',
  },
  colorNormalDimmed: 'rgb(101, 117, 128)',
  colorBackgroundDark: 'rgb(35, 35, 35)',
  colorLabelBorder: 'rgb(16, 17, 18)',
  colorLabelText: 'rgb(0, 0, 0)',
  colorDonutInternalColor: 'rgb(35, 35, 35)',
  colorDonutInternalColorHighlighted: 'rgb(255, 255, 255)',
  colorConnectionLine: 'rgb(91, 91, 91)',
  colorPageBackground: 'rgb(45, 45, 45)',
  colorPageBackgroundTransparent: 'rgba(45, 45, 45, 0)',
  colorBorderLines: 'rgb(137, 137, 137)',
  colorArcBackground: 'rgb(60, 60, 60)'
})
```

### Definitions for Data to Display 数据展示

基本结构

```js
{
	// detailedNode
  'definition title': { // we only support detailedNode for now.
  	// volume
    'display mode': { // we only support volume for now.
      'default': {}, // default renderer. this is required, and is what will be display in detailed nodes unless there are specific overrides.
      'override': {}, // can either be 'entry' if you want the entry node to be displayed differently, or the name of the renderer used (built in renderers are 'global' and 'region').
      'override2': {}, // supports multiple overrides...
    }
  }
}
```

Node详细信息

```js
{
  detailedNode: { // These definitions are for switching what the detailed node shows
    volume: { // `volume` (default mode) is already defined internally, but can be customized by passing it in again with different configuration parameters
      default: { // default is required
        // top metric in the detailed node. set to null if you want it to be blank
        //   `header` is the text header to be displayed
        //   `data` is the path to the data to display on the node object
        //   `format` is how to format the data using numeral.js
        top: { header: '% RPS', data: 'data.volumePercent', format: '0.00%' },
        // bottom metric in the detailed node. set to null if you want it to blank
        //   `header` is the text header to be displayed
        //   `data` is the path to the data to display on the node object
        //   `format` is how to format the data using numeral.js
        bottom: { header: 'ERROR RATE', data: 'data.classPercents.danger', format: '0.00%' },
        donut: {} // What fills the donut graph around the detailed node, check the donut graph header for more information
        arc: {} // What fills the arc meter inside the detailed node; if absent, no arc meter will be drawn
      },
      region: { // override for the region renderer
        top: { header: 'SERVICE RPS', data: 'data.volume', format: '0.0' }
      },
      entry: { // override for entry nodes
        top: { header: 'TOTAL RPS', data: 'data.volume', format: '0.0' }
      }
    }
  }
}
```

 Donut 图表

```js
{
  detailedNode: {
    volume: {
      default: {
        // ...
        // description of what fills the donut graph around the detailed node
        donut: {
          // OPTIONAL: If you want something different to be displayed when hovering over the donut graph,
          // you can override top and bottom inside here. If you override only one, vizceral will assume
          // that the other one should be blank.
          top: {},
          bottom: {},
          // path to the data to be displayed on the donut graph.
          // Needs to point to an object of key/value pairs, with values being decimal percentages (between 0-1)
          data: 'data.globalClassPercents',
          // by default, the coloring for the donut slices will map by key in data, and in an indeterminant order
          // If you optionally want to map to different classes and/or force a render order, override here.
          // An array of objects with key and an optional class if different than the key itself
          indices: [
            { key: 'danger' },
            { key: 'warning' },
            { key: 'normal', class: 'normalDonut' }
          ]
        }
        // ...
      }
    }
  }
}
```

Arc 图表

```js
{
  detailedNode: {
    volume: {
      default: {
        // ...
        // description of what fills the arc meter inside the detailed node. If absent, no arc meter will be drawn
        arc: {
          // OPTIONAL: If you want something different to be displayed when hovering over the arc graph,
          // you can override top and bottom inside here. If you override only one, vizceral will assume
          // that the other one should be blank.
          top: {},
          bottom: {},
          // path to the data to be displayed on the arc graph.
          // Needs to have a structure like:
          // {
          //   values: [ // Array of values
          //     { name: 'foo', value: 30 }, // Values have a value, name, and an optional overriding class. If class is not present, uses name as class name.
          //     { name: 'bar', value, 70, class 'barclass' }
          //  ],
          //   total: 100, // The total value to equal 100% of the arc graph
          //   line: 0.9 // [optional] What percent, in decimal form, to put the optional marking line.
          // }
          data: 'metadata.something',
          // the index on the above data object that gives the line position in 0 through 1 (percent decimal). If absent, no line will be drawn
          lineIndex: 'line'
        }
        // ...
      }
    }
  }
}
```

### Filters 数据筛选
```js
[
  {
    name: 'rps', // A unique name for the filter
    type: 'connection', // What object type the filter applies to ('node' or 'connection')
    passes: (object, value) => { // The function to compare a value of object to the current value
      return value < 0 || object.volume.total <= value;
    },
    value: -1 // The current value of the filter
  }
]
```

# How to Use

## Graph Data Format 图表数据格式化

本质上，一个包含这nodes（节点）和connections（连接线）的根node，的组合

```js
{
  // Which graph renderer to use for this graph (currently only 'global' and 'region')
  // 图表格式：global /region
  renderer: 'global',
  // since the root object is a node, it has a name too.
  name: 'edge',
  // OPTIONAL: The maximum volume seen recently to relatively measure particle density. This 'global' maxVolume is optional because it can be calculated by using all of the required sub-node maxVolumes.
  maxVolume: 100000,
  // list of nodes for this graph
  nodes: [
    {
      renderer: 'region',
      layout: 'ltrTree',
      // OPTIONAL Override the default layout used for the renderer.
      name: 'us-west-2',
      // Unix timestamp. Only checked at this level of nodes. Last time the data was updated (Needed because the client could be passed stale data when loaded)
      updated: 1462471847,
      // The maximum volume seen recently to relatively measure particle density
      maxVolume: 100000,
      nodes: [
        {
          name: 'INTERNET' // Required... this is the entry node
        },
        {
          name: 'apiproxy-prod',
          // OPTIONAL Override the name on the label
          displayName: 'proxy',
          // OPTIONAL Any notices that you want to show up in the sidebar, for more details check the section on notices.
          notices: [
            {
              // The title to display on the notice
              title: 'Notice about something',
              // OPTIONAL link to send the user when click on the notice
              link: 'http://link/to/relevant/thing',
              // OPTIONAL 0(default) for info level, 1 for warning level, 2 for error level (applies CSS styling)
              // 警告级别
              severity: 1
            }
          ],
          //  The class of the node. will default to 'normal' if not provided. The coloring of the UI is based on 'normal', 'warning', and 'danger', so if you want to match the UI coloring, use those class names. Any class you provide will expect to have a style 'colorClassName' available, e.g. if the class is 'fuzzy', you should also call 'vizceral.updateStyles({ colorFuzzy: '#aaaaaa' })'
          class: 'normal',
          // OPTIONAL Any data that may be handled by a plugin or other data that isn't important to vizceral itself (if you want to show stuff on the page that contains vizceral, for example). Since it is completely optional and not handled by vizceral, you technically could use any index, but this is the convention we use.
          // 自定义的数据，跟vizceral没关系
          metadata: {}
        }
      ],
      connections: [
        {
          // The source node of the connection, will log a warning if the node does not exist.
          source: 'INTERNET',
          // The target node of the connection, will log a warning if the node does not exist.
          target: 'apiproxy-prod',
          // These are the three default types/colors available in the component and the colors that are used for the nodes themselves. You are welcme to add others, or use other names instead knowing tha they may not match the UI coloring appropriately.
          metrics: {
            normal: 5000,
            danger: 5,
            warning: 0
          },
          // OPTIONAL Any notices that you want to show up in the sidebar
          notices: [],
          // OPTIONAL Any data that may be handled by a plugin or other data that isn't important to vizceral itself (if you want to show stuff on the page that contains vizceral, for example). Since it is completely optional and not handled by vizceral, you technically could use any index, but this is the convention we use.
          metadata: {}
        }
      ]
    }
  ]
}
```

# vizceral-react

```js
import Vizceral from 'vizceral-react';
<Vizceral
		 // data:数据
		 traffic={this.state.trafficData}
		 // 视图？
         view={this.state.currentView}
         viewChanged={this.viewChanged}
         viewUpdated={this.viewUpdated}
         // config:是否展示nodes标签
         showLabels={this.state.displayOptions.showLabels}
         // cinfig:筛选规则
         filters={this.state.filters}
         // event:node或connnect被选中时，obj.type = node / connection
         nodeHighlighted={(obj) => {}}
         objectHighlighted={this.objectHighlighted}
         // event:当连接被点击时
         connectionHighlighted={()=>{}}
         // event:根目录变化时触发，参数为变化的尺寸
         nodeContextSizeChanged={this.nodeContextSizeChanged}
         // 高亮nodes用到的搜索条件， string
         match={this.state.searchTerm}
         // event:高亮nodes搜索条件，匹配到结果时触发
      	 matchesFound={this.matchesFound}
      	 // config:模式 { detailedNode: 'volume' } ？
         modes={this.state.modes}
         // config:初始配置
         definitions={this.state.definitions}
         // config:样式表
         styles={styles} > ...
```

### Diff with dataview

1. 没有连线指向状态

### Diff with decision-making-system


