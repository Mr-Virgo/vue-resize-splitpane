# Vue Split Pane

Split-Pane component built with vue2.0, can be split vertically or horizontally.
Support pixel and percent

Fork from [https://github.com/PanJiaChen/vue-split-pane](https://github.com/PanJiaChen/vue-split-pane)

### How to use?
```bash
npm install vue-resize-splitpanel

#import
import splitPane from 'vue-resize-splitpanel'

# use as global component
Vue.component('vue-resize-splitpanel', splitPane);
```

### Example

```html
   <split-pane v-on:resize="resize" :min-size='20' :default-size='30' split="vertical">
      /* size若没有单位，则默认是百分比 */
      <template slot="paneL">
        A
      </template>
      <template slot="paneR">
        B
      </template>
    </split-pane>
```

```html
  //nested
   <split-pane v-on:resize="resize" :min-size='"200px"' :default-size='30' split="vertical">
      /* size若没有单位，则默认是百分比 */
      <template slot="paneL">
        A
      </template>
      <template slot="paneR">
        <split-pane split="horizontal">
          <template slot="paneL">
           B
          </template>
          <template slot="paneR">
            C
          </template>
        </split-pane>
      </template>
    </split-pane>
```

### Options
|    Property    |    Description   |   type   |	default	|
| -----------------  | ---------------- | :--------: | :----------: |
| split       | the split type |String [horizontal,vertical] |must choose one type |
| min-size         | paneL min-size  |Number/String | 10(percent) |
| default-size         | paneL default-size  |Number/String | 10(percent) |

