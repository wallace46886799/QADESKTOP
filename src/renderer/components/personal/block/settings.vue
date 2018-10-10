<template>
  <div>
      
      <div>

        <div id="leftBlock">
          <mu-auto-complete label="输入版块" :maxSearchResults="10" labelFloat :dataSource="block" filter="caseInsensitiveFilter" @change="info"/><br/>
          <mu-list class='insidelists'>
            <mu-list-item v-for="item in setting" v-bind:key='item' @click="get_block" >{{item}}</mu-list-item>
          
          </mu-list>
          <mu-flat-button label="SAVE" class="demo-flat-button" @click="save"/>
          <mu-flat-button label="GET" class="demo-flat-button" @click="get"/>
        </div>


        <div id="rightBlock">
            <mu-table multiSelectable enableSelectAll ref="table" :height='height'  @rowClick="select">
            <mu-thead>
              <mu-tr>
                <mu-th>code</mu-th>
                <mu-th>name</mu-th>
              </mu-tr>
            </mu-thead>
            <mu-tbody>
              <mu-tr v-for="item in block_code" v-bind:key="item.code">
              <mu-td>{{item.code}}</mu-td>
              <mu-td>{{item.name}}</mu-td>
              </mu-tr>
            </mu-tbody>
            </mu-table>
        </div>

        
      </div>


  </div>
</template>

<script>
import axios from 'axios'
const block = ['阿里巴巴概念', '安防', '白酒', '白马股', '参股保险', '参股民营银行', '宁德时代概念', '参股券商', '参股360', '参股万达商业',
  '参股新三板', '草甘膦', '超导', '超级品牌', '车联网', '充电桩', '创投', '大飞机', '大数据', '锂电池', '电子发票', '电子竞技',
  '电子商务', '迪士尼', '地下管网', '电力改革', '东盟自贸区', '独角兽概念', '二胎概念', '二维码识别', '风电', '分散染料', '氟化工',
  '福建自贸区', '富士康概念', '高端装备', '高送转', '高铁', '高校', '工业4.0', '工业互联网', '供应链金融', '广东自贸区', '光伏概念',
  '固废处理', '国产软件', '共享单车', '海工装备', '航运', '杭州亚运会', '核电', '互联网+', '互联网彩票', '互联网金融',
  '互联网医疗', '黄金', '沪股通', '互联网保险', '金融IC', '集成电路', '建筑节能', '家用电器', '节能环保', '节能照明', '金改',
  '京津冀一体化', '机器人概念', '基因测序', '健康中国', '军民融合', '举牌', '可燃冰', '跨境电商', '宽带中国', '蓝宝石', '冷链物流',
  '两桶油改革', '量子通信', '生态农业', '蚂蚁金服概念', '煤化工', '美丽中国', '民营医院', 'MSCI概念', '能源互联网', '农村电商',
  'O2O概念', 'OLED', 'P2P概念', '啤酒', '苹果概念', 'PM2.5', 'PPP概念', '汽车电子', '期货概念', '氢燃料电池', '禽流感', '区块链',
  '人工智能', '人脸识别', '乳业', '上海国资改革', '上海自贸区', '生物医药', '石墨电极', '石墨烯', '食品安全', '首发新股',
  '手机游戏', '水利', '水泥', 'ST板块', '生物质能', '深圳国资改革', '钛白粉', '太阳能', '碳纤维', '特钢', '特高压', '腾讯概念',
  '特色小镇', '特斯拉', '天津自贸区', '天然气', '体育产业', '通用航空', '土地流转', '脱硫脱硝', '网络直播', '网络游戏', '网约车',
  '王者荣耀', '尾气治理', '卫星导航', '物联网', '物流电商平台', '无人机', '无人驾驶', '无人零售', '污水处理', '无线充电', '微信小程序',
  '乡村振兴', '西安自贸区', '消费金融', '小金属', '小米概念', '细胞免疫治疗', '新材料概念', '新疆振兴', '新零售', '网络安全', '稀缺资源',
  '稀土永磁', '虚拟现实', '养老概念', '央企国资改革', '页岩气', '一带一路', '移动支付', '医疗改革', '医疗器械', '油品改革', '油品升级',
  '粤港澳概念', '云计算', '语音技术', '医药电商', '在线教育', '在线旅游', '债转股', '振兴东北', '智慧城市', '智能穿戴', '智能家居',
  '智能交通', '智能物流', '智能医疗', '智能音箱', '职业教育', '中韩自贸区', '中字头股票', '猪肉', '摘帽', '装配式建筑', '足球概念',
  '租售同权', '自由贸易港', '3D打印', '5G', '安徽', '北京', '福建', '广东其它', '甘肃', '广西', '贵州', '湖北', '河北', '黑龙江',
  '河南', '湖南', '海南', '吉林', '江西', '辽宁', '内蒙古', '宁夏', '浦东', '青海', '四川', '山东', '上海其它', '山西', '陕西',
  '深圳', '天津', '西藏', '新疆', '云南', '重庆', '半导体及元件', '白色家电', '保险及其他', '包装印刷', '采掘服务', '传媒', '电力',
  '电子制造', '房地产开发', '非汽车交运', '服装家纺', '纺织制造', '国防军工', '公交', '港口航运', '钢铁', '光学光电子', '环保工程',
  '化工合成材料', '化工新材料', '化学制品', '化学制药', '机场航运', '酒店及餐饮', '景点及旅游', '计算机设备', '计算机应用',
  '家用轻工', '交运设备服务', '建筑材料', '建筑装饰', '零售', '煤炭开采', '贸易', '农产品加工', '农业服务', '汽车零部件', '汽车整车',
  '其他电子', '燃气水务', '食品加工制造', '视听器材', '生物制品', '石油矿业开采', '通信服务', '通信设备', '通用设备', '物流', '新材料',
  '医疗器械服务', '饮料制造', '园区开发', '仪器仪表', '有色冶炼加工', '银行', '医药商业', '养殖业', '综合', '证券', '中药', '专用设备',
  '造纸', '种植业与林业', '传播与文化产业', '采掘业', '电力、煤气及水的生产和供应业', '房地产业', '金融、保险业', '交通运输、仓储业',
  '建筑业', '农、林、牧、渔业', '批发和零售贸易', '社会服务业', '信息技术业', '综合类']
const setting = []
const monitorCode = []
export default {
  data () {
    return {
      setting,
      block,
      message: '',
      height: '580px',
      block_code: [],
      block_ref: '',
      monitorCode
      // myfilter (searchText, key) {
      //   return key && key.toLowerCase().indexOf('m') !== -1 && key.toLowerCase().indexOf('y') !== -1
      // }
    }
  },
  methods: {
    info (message) {
      if (this.setting.indexOf(message) === -1) {
        this.setting.push(message)
      }
    },
    get_block (message) {
      this.block_ref = message.srcElement.innerText
      axios.get('http://localhost:8010/stock/block?block=' + this.block_ref)
        .then(response => {
          // console.log(response.data['result'])
          this.block_code = response.data['result']
        }
        )
    },
    select (id) {
      this.monitorCode.push({ 'block': this.block_ref, 'code': this.block_code[id]['code'], 'name': this.block_code[id]['name'] })
    },
    save () {
      console.log({ 'block': JSON.stringify(this.monitorCode) })
      axios.post('http://localhost:8010/user/blocksetting?block=' + JSON.stringify(this.monitorCode))
    },
    get () {
      axios.get('http://localhost:8010/user/blocksetting')
        .then(response => {
          let tempData = response.data['block']
          console.log(tempData)
          console.log(tempData[1]['block'])
          console.log(this.setting)
          for (var i = 0; i < tempData.length; i++) {
            if (this.setting.indexOf(tempData[i]['block']) === -1) {
              this.setting.push(tempData[i]['block'])
            }
          }
          // console.log(tempData.split(','))
          // this.block_code
        })
    }
  }
}
</script>




<style lang="css">

#leftBlock{
  width: 20%;
  height: 100%;
  float: left;
  display: inline-block;
}
.mu-auto-complete .mu-text-field-content{
  width: 100%;
}

#rightBlock{
  width: 80%;
  height: 700px;
  float: right;
}
#view{
  width: 100%;
  height: 100%;
}
.insidelists{
  width: 100%;
  height: 510px;

}
.demo-flat-button{
  padding-top: 2%;
}


</style>