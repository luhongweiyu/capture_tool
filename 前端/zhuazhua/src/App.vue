<template>
  <div style="top: 0%;left: 0%; position: absolute;">
    <div style="margin-bottom: 20px;width: 200px;height: 125px;display: inline-block">
      <el-upload v-model:file-list="fileList" class="upload-demo" drag :auto-upload="true" :before-upload="上传前"
        style="">
        <div class="el-upload__text">
          拖动图片文件到这 <br />或<em>点击载入</em>
        </div>
      </el-upload>
    </div>
    <div style="display: inline-block">
      <textarea v-model="生成内容2" cols="50" rows="2"></textarea>
      <br />
      <textarea v-model="生成内容" cols="50" rows="6"></textarea>
    </div>
    <br />
    <!-- {{ 坐标a.x }},{{ 坐标a.y }} -->
    <!-- {{ 坐标s.x }},{{ 坐标s.y }} -->
    <div style="display: inline-block;width: 50px;">
      宽:{{ 坐标s.x - 坐标a.x }}
    </div>
    <div style="display: inline-block;width: 50px;">
      高:{{ 坐标s.y - 坐标a.y }}
    </div>

    <button @click="生成">生成</button>
    <!-- {{ 当前标签页name }} -->
    <el-tabs v-model="当前标签页name" type="card" editable class="demo-tabs" @edit="handleTabsEdit">
      <el-tab-pane v-for="(item, i) in editableTabs" :key="item.name" :label="item.title" :name="item.name">
        <!-- {{ item.name }} -->
        <div v-if="!item.图片路径">
          {{ item.content }}

        </div>
        <div style="width: 100vw;left: 0px;top:0px;">
          <div style="width: 2000px;;left: 0px;top:0px;">
            <!-- <img @mousemove="鼠标指向" @click="rectClick" @keydown.enter="按下键盘" placeholder="按任意键" id="image-preview" -->
            <!-- :src="item.图片路径" alt="预览图片" style="width: auto;height: auto;left: 0px;top:0px;" ismap> -->
            <canvas style="border: #00ffff 1px solid;" @mousemove="鼠标指向" @keydown="按下键盘" :id="'canvas' + item.name"
              :ref="(el) => loadpic(el, item.图片路径, item.name)" tabindex="0">
            </canvas>
          </div>

        </div>

        <!-- style="display: none;" -->


        <!-- <div id="drop-area">将图片拖拽到这里</div> -->


      </el-tab-pane>
    </el-tabs>


    <!-- <img src="./11_13_17_26_54.bmp" @load="onImageLoad" alt="Sample Image"> -->
    <!-- <canvas ref='ccc' width="500" height="500"></canvas> -->

  </div>
  <div style="top: 0%;right: 0%; position: fixed;border: #000000 5px solid;">
    <button @click="清除">清除</button>
    坐标:{{ 颜色.x }},{{ 颜色.y }}颜色:{{ 颜色.r }}{{ 颜色.g }}{{ 颜色.b }}
    <br />
    q:
    <input v-model="坐标a.x" style="width:50px;" />
    <input v-model="坐标a.y" style="width:50px;" />
    e:
    <input v-model="坐标s.x" style="width:50px;" />
    <input v-model="坐标s.y" style="width:50px;" />


    <table>
      <tbody>
        <tr v-for="y in [-10, -9, -8, -7, -6, -5, -4, -3, -2, -1, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]   ">
          <td v-for="x in [-10, -9, -8, -7, -6, -5, -4, -3, -2, -1, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]   "
            style="padding: 0 ;border:0px solid #00ffff;width: 10px;height: 10px;">
            <!-- <div v-if="放大镜[x + y * 11]" :style="{background-color: 放大镜[x + y * 11]}"> -->
            <div v-if="放大镜[x + y * 21]" :style="{ 'background': 放大镜[x + y * 21], width: '10px', height: '10px' }">

              <!-- {{ 放大镜[x + y * 11] }} -->
              <div v-if="x == 0 && y == 0" style="margin:1px; border: 1px solid #ff00ff;height: 8px;">
                <!-- x -->
              </div>

            </div>


            <!-- {{x}},{{y}} -->
          </td>
        </tr>

      </tbody>
    </table>



    <button @click="清空选择颜色">清空</button>
    <div v-for="(点, i ) in 已选颜色">


      <!-- {{点}} -->
      <!-- 多选框  -->
      <!-- 坐标  -->
      <!-- 颜色  -->
      <!-- 色差 -->
      <!-- 显示颜色  -->
      <!-- {{ 点.x }} -->
      <!-- {{ 点.y }} -->
      <!-- {{ 点.color }} -->



      <input v-model="点.偏色" :style="{ 'background': '#' + 点.color, width: '20px' }" />
      <Xzk v-model:up_value="点.选中" :showtext="(i + '').padStart(2, '0')" :default_value="true"
        :style="{ width: '20px' }" />
      <input v-model="点.x" style="width:40px ;" />
      <input v-model="点.y" style="width:40px ;" />
      <input v-model="点.color" style="width:60px ;" />
      <input v-model="点.偏色" style="width:60px ;" />


    </div>

    <!-- <checkbox v-model:up_value="ts['ui_场景_巫医']" showtext="巫医" :default_value="true" />间隔: -->

  </div>
</template>

<script lang="ts" setup>


// document.body.onkeydown = function (event) {
//     var e = window.event || event;
//     if(e.preventDefault){
//         e.preventDefault();
//     }else{
//         window.event.returnValue = false;
//     }
// }

import { ref } from 'vue'
import type { TabPaneName } from 'element-plus'
import { UploadFilled } from '@element-plus/icons-vue'
import Xzk from "./components/选择框.vue";
import { ta } from 'element-plus/es/locale';
// component('Xzk', Xzk)
const 颜色 = ref({})
const x = ref("")
const y = ref("")
const 图片加载记录 = ref({})
const 放大镜 = ref([])
const 已选颜色 = ref([])
const 坐标a = ref({})
const 坐标s = ref({})
const 生成内容 = ref("")
const 生成内容2 = ref("")
const fileList = ref([])
let 标签页数量 = 2
const 当前标签页name = ref('2')

const editableTabs = ref([
  // {
  //   title: 'Tab 1',
  //   name: '1',
  //   content: 'Tab 1 content',
  //   img_dom: ref(),
  //   图片路径: './11_13_17_26_54.bmp'
  // },
  // {
  //   title: 'Tab 2',
  //   name: '2',
  //   content: 'Tab 2 content',
  //   img_dom: ref(),
  //   图片路径: './11_13_19_41_50.bmp'
  // },
])
const 上传前 = (rawFile) => {
  console.log(rawFile.name)
  if (rawFile.type.startsWith('image/')) {
    const reader = new FileReader();
    reader.onload = (e) => {
      // imageSrc.value = e.target.result;
      addTab(rawFile.name, e.target.result)
    };
    reader.readAsDataURL(rawFile);
  } else {
    alert('请上传图片文件');
    return false;
  }
  return false
}
const 鼠标历史 = {}
const 鼠标指向 = (event) => {
  if (鼠标历史.x === event.offsetX && 鼠标历史.y === event.offsetY) {
    return
  }
  鼠标历史.x = event.offsetX
  鼠标历史.y = event.offsetY
  x.value = event.offsetX
  y.value = event.offsetY
  显示颜色(x.value, y.value)
}
const 显示颜色 = (x, y) => {
  let rgb = getColor(x, y)
  颜色.value.x = x
  颜色.value.y = y
  // 颜色.rgb= 
  颜色.value.r = rgb.r//.toString(16).padStart(2, '0').toUpperCase()
  颜色.value.g = rgb.g//.toString(16).padStart(2, '0').toUpperCase()
  颜色.value.b = rgb.b//.toString(16).padStart(2, '0').toUpperCase()
  for (let px = -10; px <= 10; px++) {
    for (let py = -10; py <= 10; py++) {
      // if 
      let rgb = getColor(x + px, y + py)
      // 放大镜.value[px + py * 11] = ref(rgb.r + "," + rgb.g + "," + rgb.b)
      放大镜.value[px + py * 21] = '#' + rgb.r + rgb.g + rgb.b
    }

  }
}


const addTab = (名称, 图片路径) => {
  const newTabName = `${++标签页数量}`
  editableTabs.value.push({
    title: 名称 || 'New Tab',
    name: newTabName,
    content: 'New Tab content',
    // img_dom: ref(),
    图片路径: 图片路径,
  })
  当前标签页name.value = newTabName
}
window.e_daoru = function (名称) {
  console.log("截图被调用了")
  addTab(名称, "./tmp.bmp?" + Math.random())
}
const handleTabsEdit = (
  targetName: TabPaneName | undefined,
  action: 'remove' | 'add'
) => {
  if (action === 'add') {
    const newTabName = `${++标签页数量}`
    editableTabs.value.push({
      title: 'New Tab',
      name: newTabName,
      content: 'New Tab content',
    })
    当前标签页name.value = newTabName
  } else if (action === 'remove') {
    const tabs = editableTabs.value
    let activeName = 当前标签页name.value
    if (activeName === targetName) {
      tabs.forEach((tab, index) => {
        if (tab.name === targetName) {
          const nextTab = tabs[index + 1] || tabs[index - 1]
          if (nextTab) {
            activeName = nextTab.name
          }
        }
      })
    }

    当前标签页name.value = activeName
    editableTabs.value = tabs.filter((tab) => tab.name !== targetName)
  }
}

const bbb = () => {
  console.log('按了一下')
}


const 按下键盘 = (event: all) => {

  // key = w, keyCode = 87
  // key = s, keyCode = 83
  // App.vue:292 a 177
  // key = a, keyCode = 65
  // key = d, keyCode = 68
  // key = q, keyCode = 81
  // key = e, keyCode = 69
  // console.log('按了一下')
  const key = event.key;
  const keyCode = event.keyCode;
  if (key === 'ArrowUp' || keyCode === 38 || keyCode === 87) {
    y.value = y.value - 1
    显示颜色(x.value, y.value)
    event.preventDefault();
  } else if (key === 'ArrowDown' || keyCode === 40 || keyCode === 83) {
    y.value = y.value + 1
    显示颜色(x.value, y.value)
    event.preventDefault();
  } else if (key === 'ArrowLeft' || keyCode === 37 || keyCode === 65) {
    x.value = x.value - 1
    显示颜色(x.value, y.value)
    event.preventDefault();
  } else if (key === 'ArrowRight' || keyCode === 39 || keyCode === 68) {
    x.value = x.value + 1
    显示颜色(x.value, y.value)
    event.preventDefault();
  } else if (key === 'q' || keyCode === 81) {
    console.log('q', x.value)
    坐标a.value.x = x.value
    坐标a.value.y = y.value
  } else if (key === 'e' || keyCode === 69) {
    坐标s.value.x = x.value
    坐标s.value.y = y.value

  } else if (key === ' ' || keyCode === 32) {
    console.log("按下了空格")
    console.log(event)
    let c = 颜色.value
    已选颜色.value.push({ 选中: true, x: c.x, y: c.y, color: '' + c.r + c.g + c.b })
    event.preventDefault();

  } else if (key === 'Enter' || keyCode === 13) {
    生成()

  }

  console.log(`按下键: key = ${key}, keyCode = ${keyCode}`);
}


// window.addEventListener('keydown', 按下键盘);

// const ccc = ref()

const loadpic = (ccc, img_src, tab_name) => {
  if (tab_name !== 当前标签页name.value) {
    // console.log(img_src, "不是当前tab")
    return
  }
  // console.log(img_src, ccc)
  if (!ccc) {
    console.log('没有ccc')
    return
  }
  图片加载记录.value[tab_name] = ccc
  // console.log(ccc)
  let img = new Image();
  // img.crossOrigin = "anonymous"; // 图片允许跨域，否则不能拿到图片的数据
  img.src = img_src;//"./11_13_17_26_54.bmp";
  const canvas = ccc//document.getElementById("canvas");
  const ctx = canvas.getContext("2d");
  // if (img.complete) { // 如果图片已经存在于浏览器缓存，直接调用回调函数  
  //   console.log("缓存图片加载完毕2")
  //   canvas.width = img.width;
  //   canvas.height = img.height;
  //   ctx.drawImage(img, 0, 0,img.width,img.height);
  //   return; // 直接返回，不用再处理onload事件  
  // }
  img.onload = () => {
    canvas.width = img.width;
    canvas.height = img.height;
    console.log("图片加载完毕")
    // canvas绘制图片 ctx.drawImage(图片, x位置, y位置，图片宽度，图片高度)
    ctx.drawImage(img, 0, 0, img.width, img.height);
    // 图片设置成隐藏
    // img.style.display = "none"; // 只显示canvas绘制的图片，不显示img图片
  };
}
const getColor = (x, y) => {
  // 图片加载记录

  const 画板 = 图片加载记录.value[当前标签页name.value]

  if (!画板) return;

  const ctx = 画板.getContext('2d');
  // const x = 50; // 指定的 x 坐标
  // const y = 50; // 指定的 y 坐标

  if (!ctx) return;
  ctx.canvas.willReadFrequently = true;
  const pixelData = ctx.getImageData(x, y, 1, 1).data;
  const r = pixelData[0].toString(16).padStart(2, '0').toUpperCase();
  const g = pixelData[1].toString(16).padStart(2, '0').toUpperCase();
  const b = pixelData[2].toString(16).padStart(2, '0').toUpperCase();
  const a = pixelData[3];
  return { r: r, g: g, b: b }
  // return `rgba(${r}, ${g}, ${b}, ${a / 255})`;
};
const 生成 = () => {

  let ss1 = []
  let ss2 = []
  for (let i = 0; i < 已选颜色.value.length; i++) {
    if (已选颜色.value[i].选中 == true) {
      let s = ''
      s = s + (已选颜色.value[i].x - 已选颜色.value[0].x)
      s = s + "|" + (已选颜色.value[i].y - 已选颜色.value[0].y)
      s = s + "|" + 已选颜色.value[i].color
      if (已选颜色.value[i].偏色) {
        s = s + '-' + 已选颜色.value[i].偏色
      }
      ss1.push(s)


      s = ''
      s = s + (已选颜色.value[i].x)
      s = s + "|" + (已选颜色.value[i].y)
      s = s + "|" + 已选颜色.value[i].color
      if (已选颜色.value[i].偏色) {
        s = s + '-' + 已选颜色.value[i].偏色
      }
      ss2.push(s)

    }
  }
  ss1.join(',')
  ss2.join(',')
  let rect = [坐标a.value.x, 坐标a.value.y, 坐标s.value.x, 坐标s.value.y]
  if (!rect[0]) {
    rect[0] = 0
  }
  if (!rect[1]) {
    rect[1] = 0
  }
  if (!rect[2]) {
    rect[2] = 图片加载记录.value[当前标签页name.value].width
  }
  if (!rect[3]) {
    rect[3] = 图片加载记录.value[当前标签页name.value].height
  }

  let point = ''
  if (已选颜色.value.length > 0) {
    point = `point={${已选颜色.value[0].x},${已选颜色.value[0].y}}`
  }

  let s = `{rect={${rect[0]},${rect[1]},${rect[2]},${rect[3]}},${point},${ss1.join(',')}}`
  生成内容.value = s

  s = `{rect={${rect[0]},${rect[1]},${rect[2]},${rect[3]}},${point},${ss2.join(',')}}`
  生成内容2.value = s




}
const 清除 = () => {

  坐标a.value = {}
  坐标s.value = {}
  生成内容.value = ""
  生成内容2.value = ""
}
const 清空选择颜色 = () => {
  已选颜色.value = []
}

</script>

<style>
.demo-tabs>.el-tabs__content {
  padding: 0pz;

  color: #6b778c;
  font-size: 32px;
  font-weight: 600;
  width: 100%;
}

body {

  background-color: #222;

  color: #ddd;

}
</style>