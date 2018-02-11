# slider
  slider基于vue开发的轮播图组件
	* pc端可使用
	* 移动端touch可使用
	* 组件自行判断终端设备,移动端时会自适应,开启touch事件
	* 窗口变化时,组件自行重新渲染,适应新的屏幕尺寸
	* 组件由外部父级盒子决定自身宽高
# 安装
```cmd
npm install slider-v --sava
```
# 使用
```javascript
import slider from 'slider-v'

components:{
 slider
}
```
```html
<slider>
 <div v-for="item in bannerList">
  <img :src="item.src">
 </div>
</slider>
<!--或者可以-->
<slider>
 <div v-for="item in bannerList">
  <a :href='item.href'>
   <img :src="item.src">
  </a>
 </div>
</slider>
```
# 参数
```html
<slider :sliderList='sliderList' :autoplay='true' :sliderType="'slide'">
 <!--所有参数按此逻辑填写-->		
</slider>
```
### sliderList
  **必填数据**<br>
	接受数组形式数据,此为唯一必填参数, 该参数是为了在数据改变时, 能重新渲染组件<br>
	轮播数据, 数组, 默认为空数组
### autoplay
	是否自动播放滚动, 布尔值, 默认true(自动播放)
### time
	切换轮播时间, 数字, 默认3000(ms)
### sliderType
	轮播方式, 字符串, 默认为'slide'
* 'slide' 左右滑动
* 'fade' 淡入淡出
* '3d' 3d形式轮播(非真3d) 3d方式中,中央显示图片的宽度为最外围盒子的一半
### arrowsShow
	是否显示左右箭头, 布尔值, 默认true(显示)
### dotsShow
	是否显示导航点, 布尔值, 默认true(显示)
### hoverStop
	是否在鼠标移入时,停止轮播图滚动(在开启自动轮播时有效) 布尔值, 默认true(不停止)<br>
	// 在轮播图沾满全屏时,鼠标始终会在轮播图内,此时可开启

# 回调
```html
<slider @currentIndex='current'>
	<!--所有回调按此逻辑填写,current为你定义的回调函数名-->
</slider>
```
```javascript
methods: {
	current(index) {
		console.log(index)
		// index为回调值
	}
}
```
### currentIndex
	返回当前轮播位置的索引值, 索引值以0开始
