# mw

## page——list

``` js
inbiz.on("code",()=>{
    let page=Page()
    page.init()
    page.bindEvents()
})

function Page(){
    let self={};
    self.init=function(){
        
    }
    self.bindEvents=function(){
         inbiz("datatable").configEvent("onParamFormat",self.onTableParamFormat);
         inbiz("datatable").configEvent("onColumnsFormat",self.onTableColumnsFormat);
         inbiz("datatable").configEvent("onBeforeOpen",self.onTableBeforeOpen);
    }
     /**列格式化事件 */
    self.onTableColumnsFormat = function (value, record, index, fieldName) {
     if (fieldName == "xxx" && value /**xxx */) {
        const btnRender = inbiz.utils.toReactNode(
          '<Button onClick="click" type="link">code</Button>',
          { type: "link" }
        );
        return btnRender({
          code: value,
          click: function () {
           //to do
          },
        });
      }
      return value;
    };
    //条件过滤
    self.onTableParamFormat = function (data) {
      var modelname = inbiz("datatable").modelname;
      //添加组织机构筛选条件
      data = gxp.SmsDataFilter(data, modelname);
      data.conditions.lastItem.condition.push(gxp.createCondition(modelname,"material_status",'1',"!="));
      return data;
    };
    //子窗体打开前
    self.onDataTableBeforeOpen = function (data) {
      var actionInfo = data.actionInfo;
      if (actionInfo.actionType != "custom" /*自定义按钮*/) {
        return data;
      } 
      //验证是否选中数据
      let rows = inbiz("datatable").getSelectedRow()||[];
      if (rows.length==0) {
        // 获取多语言
        let message = inbiz.utils.getMessage(
          "i18n-mv7b1ow8" /*请至少选中一行数据*/
        );
        inbiz.alert.error({ content: message });
        return false;
      }
      if (rows.length != 1) {
        // 获取多语言
        let message = inbiz.utils.getMessage(
          "i18n-zhlvb93d" /*请选择一条数据*/
        );
        inbiz.alert.error({ content: message });
        return false;
      }
      if (actionInfo.operationflag == "a" /**xx */) {
         data.id = rows[0].id; 
      } else if (actionInfo.operationflag == 'sf' /**cxx */) {
          actionInfo.sourceId = rows[0].id;
          let item = gxp.workFlowInfos.find((item) => {
            return item.page == "pageName";
          });
          data.processId = item.processId; 
      }
      return data;
    };
    return self;
}
```
