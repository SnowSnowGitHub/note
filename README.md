#### jquery中innerWidth()，outerWidth()，outerWidth(true)和width()的区别

```
var a = 元素本身的宽度；

width() = a；

innerWidth() = a+padding;

outerWidth() = a+padding+border;

outerWidth(true) = a+padding+border+margin;

在jQuery中，
width()方法用于获得元素宽度；
innerWidth()方法用于获得包括内边界（padding）的元素宽度，
outerWidth()方法用于获得包括内边界(padding)和边框(border)的元素宽度，
如果outerWidth()方法的参数为true则外边界(margin)也会被包括进来，即获得包括外边框(margin)、内边界(padding)和边框(border)的元素宽度。
同理，innerHeight方法与outerHeight方法也是用同样的方法计算相应的高度。
所以说：对于同一个元素应该是：
width()<=innerWidth()<=outerWidth()<=outerWidth(true);
```



#### mui插件 上拉刷新和下拉加载的注意点 (mui 乐淘购物车页面)

- [ ] ```
      html中
      <!--下拉刷新容器-->
      <div id="refreshContainer" class="mui-content mui-scroll-wrapper">
        <div class="mui-scroll">
          <!--数据列表-->
          <ul class="mui-table-view mui-table-view-chevron">
            
          </ul>
        </div>
      </div>



      首先需要在页面引用这个js,这个是基本的就不多说了，然后要进行初始化：

      	mui.init({
      		swipeBack : true,//右滑关闭
      		pullRefresh : {
      			container : '#refreshContainer',//下拉刷新容器标识，querySelector能定位的css选择器均可，比如：id、.class等
      			down : {
      				height : 50,//可选,默认50.触发下拉刷新拖动距离,
      				range : 100, //可选 默认100px,控件可下拉拖拽的范围
      				offset : 30, //可选 默认0px,下拉刷新控件的起始位置
      				color : '#2BD009', //可选，默认“#2BD009” 下拉刷新控件颜色
      				style : 'circle',//可选，下拉刷新样式，目前支持原生5+ ‘circle’ 样式
      				auto : false,//可选,默认false.自动下拉刷新一次
      				contentdown : "下拉可以刷新",//可选，在下拉可刷新状态时，下拉刷新控件上显示的标题内容
      				contentover : "释放立即刷新",//可选，在释放可刷新状态时，下拉刷新控件上显示的标题内容
      				contentrefresh : "正在刷新...",//可选，正在刷新状态时，下拉刷新控件上显示的标题内容
      				callback : pulldownRefresh//必选，刷新函数，根据具体业务来编写，比如通过ajax从服务器获取新数据(******必选******)
      			},
      			up : {
      				height : 50,//可选.默认50.触发上拉加载拖动距离
      				auto : false,//可选,默认false.自动上拉加载一次
      				contentrefresh : "正在加载...",//可选，正在加载状态时，上拉加载控件上显示的标题内容
      				contentnomore : '没有更多数据了',//可选，请求完毕若没有更多数据时显示的提醒内容(******最好选******)
      				callback : pullupRefresh//必选，刷新函数，根据具体业务来编写，比如通过ajax从服务器获取新数据(******必选******)
      			}
      		}
      	});


      let page=1
      function pulldownRefresh()={
              setTimeout(function(){
                  //加载数据
                  getCartProduct();
                  //执行完查询之后必须结束下拉刷新
                  mui('#refreshContainer').pullRefresh().endPulldownToRefresh();
                  //重置上拉 下拉结束的时候 要重置上拉的加载效果 如果不进行重置的话 就无法再次上拉加载了
                  mui('#refreshContainer').pullRefresh().refresh(true);
                  //page也要重置为1  下次加载的时候 从第一页开始加载
                  page=1;
              },1000)
      }
          
      function pullupRefresh()={
              page++
              setTimeout(function(){
                  $.ajax({
                      url:'/cart/queryCartPaging',
                      data:{
                          page:page,
                          pageSize:5
                      },
                      success(res){
                          console.log(res)
                          if(res.data.length==0){
                              mui('#refreshContainer').pullRefresh().endPullupToRefresh(true); //参数为true代表没有更多数据了。
                          }else{
                              let html=template('cartProductTpl',res)
                              // console.log(html)
                              $('#main ul').append(html)
                              //结束上拉加载
                              mui('#refreshContainer').pullRefresh().endPullupToRefresh(); //refresh completed
                          }
                      }
                  })
              },1000)
      }


      ```

