<template>
  <div class="hello">
  <!-- 搜索 -->
    <div>
      <a-input-search placeholder="Search by keywords or content cid"
          style="width: 30%;"
          size="large"
          class="mg20"
          enter-button @search="onSearch"/> 
    </div>

  <!-- 表格 -->
    <a-table :columns="columns" :data-source="tableData"
        style="margin-left: 40px; margin-right: 40px"
        :pagination="pagination" class="mg20" 
        :rowKey="(r, i)=>{return r.cid}"       
        @change="handleTableChange"
      >
      <div slot="fileName" slot-scope="fileName, record">
        <a @click="showDetails(fileName, record)"> {{fileName}} </a>
      </div>
      <div slot="size" slot-scope="size">
        {{tosize(size)}}
      </div>
      <div slot="cid" slot-scope="cid">
        {{fix(cid)}}
      </div>

      <span slot="action" slot-scope="text, record">
        <a @click="startDown(text, record)"> <a-icon type="download" /> </a>
      </span>
    </a-table>

  <a-modal
      v-model="detailVisible"
      title="iso details"
      :confirmLoading="confirmLoading"
      width="50%"
      okText="download"
      @ok="handleOk"
      @cancel="handleCancel"
    >
      <p>fileName: {{detailsRecord.fileName}}</p>
      <p>fileSize: {{tosize(detailsRecord.size)}}</p>
      <p>cid: {{detailsRecord.cid}}</p>
    </a-modal>
  </div>
</template>

<script>
import axios from 'axios'
import moment from 'moment'

const columns = [
  {
    title: 'fileName',
    dataIndex: 'fileName',
    key: 'fileName',
    width: '30%',
    scopedSlots: { customRender: 'fileName'},

  },
  {
    title: 'size',
    dataIndex: 'size',
    key: 'size',
    ellipsis: true,
    width: '20%',
    scopedSlots: { customRender: 'size'},

  },
  {
    title: 'cid',
    dataIndex: 'cid',
    key: 'cid',
    ellipsis: true,
    scopedSlots: { customRender: 'cid'},
  },
  {
    title: 'createdTime',
    dataIndex: 'createdtime',
    key: 'createdtime',
    width: '20%',
  },
  {
    title: 'Action',
    key: 'action',
    scopedSlots: { customRender: 'action' },
    width: '10%',

  },
];

const tabData = [
  /* 1 */
{
    "fileName" : "debian-live-10.0.0-i386-lxde.iso",
    "size" : 2377711616,
    "cid" : "QmWbFgsrmvtZ5tRdz64m1mAYMYpQe6WYjtzB9r2pQGHYTo",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 2 */
{
    "fileName" : "debian-live-10.0.0-i386-standard.iso",
    "size" : 870318080,
    "cid" : "QmYTTfhWpY25xdRPDajFJFtwwncAV9qbrAfpe4vZfikTRN",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 3 */
{
    "fileName" : "debian-live-10.0.0-i386-mate.iso",
    "size" : 2470903808,
    "cid" : "QmZv5DMVw8zEm4RopTR2wccy74hbEBpDZcXdyqbzGsVaT3",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 4 */
{
    "fileName" : "debian-live-10.0.0-i386-xfce.iso",
    "size" : 2351726592,
    "cid" : "QmaHbafkyPK7Mj8gRUfZDNXfAU8LZK2oqno28Lg5cnoHCH",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 5 */
{
    "fileName" : "debian-live-10.0.0-i386-gnome.iso",
    "size" : 2520989696,
    "cid" : "QmV6UBtbBrNTFPYgmAw81qccjL3WYrmz8GqGf2EfZjoxVP",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 6 */
{
    "fileName" : "debian-live-10.0.0-i386-lxqt.iso",
    "size" : 2487599104,
    "cid" : "QmdMjJEsiw88DNdTTrZ1ibFrJfSpkCctfE7o6nhyVBXp4N",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 7 */
{
    "fileName" : "debian-live-10.0.0-i386-cinnamon.iso",
    "size" : 2494922752,
    "cid" : "QmUyQDHnaomDdc1J8GBUYAzjy6U4ffkRXpCcPMaVgfRbi3",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 8 */
{
    "fileName" : "debian-live-10.0.0-i386-kde.iso",
    "size" : 2740715520,
    "cid" : "Qme34TpN8z5HFGmKMjdxcwhdduqovp3gELjtAsThAfKMYh",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 9 */
{
    "fileName" : "debian-live-10.0.0-amd64-standard.iso",
    "size" : 865075200,
    "cid" : "QmcTSDfpiRyCgfVyMv7L2uww55qP17um2V72wU2Wq581SB",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 10 */
{
    "fileName" : "debian-live-10.0.0-amd64-kde.iso",
    "size" : 2701131776,
    "cid" : "QmcyA9HC2rsSLVHPnACZVUo88yEH8XhWK8HasLrHTKuDAF",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 11 */
{
    "fileName" : "debian-live-10.0.0-amd64-gnome.iso",
    "size" : 2494922752,
    "cid" : "QmPD1yB9NWGqCz6Pr9WyqJdzkdHoERTK7R9FVa5reqMiMw",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 12 */
{
    "fileName" : "debian-live-10.0.0-amd64-cinnamon.iso",
    "size" : 2466250752,
    "cid" : "QmQHDdEYEvGsct4LHFMGCxqAPLuK7tkMfxvVhx1cjzrhSW",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 13 */
{
    "fileName" : "debian-live-10.0.0-amd64-lxqt.iso",
    "size" : 2459025408,
    "cid" : "QmYJzPqqN2UJwNXyQFwvFvg5SJEoeGg7Q7PcPRM2mBPpmq",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 14 */
{
    "fileName" : "debian-live-10.0.0-amd64-lxde.iso",
    "size" : 2349416448,
    "cid" : "QmNihGnHabAg5xgytcpGJ4wn4QWrTaW7N6MVbJ7uHJ5egw",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 15 */
{
    "fileName" : "debian-live-10.0.0-amd64-xfce.iso",
    "size" : 2325200896,
    "cid" : "QmdSA7HsUT9rbfnBXxaXfbC4trXP7TB9gjxcWp6G7LkXjx",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 16 */
{
    "fileName" : "debian-live-10.0.0-amd64-mate.iso",
    "size" : 2444689408,
    "cid" : "QmaP14hBGdgNiBuB7a4MNeq4STVV2meuTNEpoAgtbXmmog",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 17 */
{
    "fileName" : "debian-9.4.0-mips-netinst.iso",
    "size" : 265627648,
    "cid" : "QmTWndFMfJTSJ9BEQei2SCwmzixz8DhPx7JUcrfgSyzVq8",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 18 */
{
    "fileName" : "debian-9.4.0-mips-xfce-CD-1.iso",
    "size" : 678770688,
    "cid" : "QmcxxT8THzfrwrHXwaGU9QkQ33MS2RfA5XMaJb8XUWMmNo",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 19 */
{
    "fileName" : "debian-9.4.0-mips-DVD-1.iso",
    "size" : 4481525760,
    "cid" : "QmPpNo4cb3eBT9h4pwBeZV8dDQkk31X1Bcn4t45aVS4p6Z",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 20 */
{
    "fileName" : "debian-9.4.0-armel-netinst.iso",
    "size" : 382144512,
    "cid" : "QmQNn7kAvnSngirrjrPH59r6fNCgDKi2wXw92swX76zeir",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 21 */
{
    "fileName" : "debian-9.4.0-armel-xfce-CD-1.iso",
    "size" : 675000320,
    "cid" : "QmRuntbCPkPy94mDZCeWPa4HX7uWc8pDZYz2FxVBw8D3Lk",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 22 */
{
    "fileName" : "debian-9.4.0-armel-DVD-1.iso",
    "size" : 4668905472,
    "cid" : "QmZgJ22bfcqdBXmkmDuHsffWRV1Y996CyqJ8HWM2EqTgVF",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 23 */
{
    "fileName" : "debian-9.4.0-mipsel-netinst.iso",
    "size" : 318957568,
    "cid" : "QmS84JoGJjso8pm8vcuTYCecX2Snj1udS9PdDLYGCFG1YW",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 24 */
{
    "fileName" : "debian-9.4.0-mipsel-xfce-CD-1.iso",
    "size" : 674848768,
    "cid" : "QmZafbCeE9mCjqNddD5GLjYvpXH4M6WUr12kZAEQKtXSL3",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 25 */
{
    "fileName" : "debian-9.4.0-mipsel-DVD-1.iso",
    "size" : 4595419136,
    "cid" : "QmWwFSMmf8BJ5a9eFZYt4b3vjP8qaKL4TyqoJ7CV9jRXfE",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 26 */
{
    "fileName" : "debian-9.4.0-armhf-xfce-CD-1.iso",
    "size" : 679116800,
    "cid" : "QmZ9mARUqpSRHZxEvjZa3XMfnsrzRXM2dsHR3tNt4uWPWG",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 27 */
{
    "fileName" : "debian-9.4.0-armhf-netinst.iso",
    "size" : 368603136,
    "cid" : "QmNVYgUaM99fo87eY3DQd27VBeUYDxCMuPAiRgJvHHf4rr",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 28 */
{
    "fileName" : "debian-9.4.0-armhf-DVD-1.iso",
    "size" : 4673329152,
    "cid" : "QmdZtKTWSw4orZB3WZxrHHmaX7Z1dZPigNN8vi2wPNvJVC",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 29 */
{
    "fileName" : "debian-9.4.0-s390x-xfce-CD-1.iso",
    "size" : 677449728,
    "cid" : "QmUrEFYafBATvH352zAxMjoSqjyzUJuLiDFHpFQizrETrj",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 30 */
{
    "fileName" : "debian-9.4.0-s390x-netinst.iso",
    "size" : 169988096,
    "cid" : "QmYnx3PeCNdC2bNpsqRN99aFb3YWfqtzA8BhddQsNRfBee",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 31 */
{
    "fileName" : "debian-9.4.0-s390x-DVD-1.iso",
    "size" : 4516747264,
    "cid" : "QmUK9Hrp4dbc5CYNwpipjPfWSEVs3PQUKfdo6VYhBQyfHY",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 32 */
{
    "fileName" : "debian-9.4.0-i386-xfce-CD-1.iso",
    "size" : 678428672,
    "cid" : "QmYKGSkc6UGBX3VMx2qZrN2eEsFtAZxfnzbzLM64cacaHi",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 33 */
{
    "fileName" : "debian-9.4.0-i386-netinst.iso",
    "size" : 395313152,
    "cid" : "QmYm2F5iVZ5w1xkXTwcdZ1sTYPUocr3sKNNsdVw54KjMut",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 34 */
{
    "fileName" : "debian-mac-9.4.0-i386-netinst.iso",
    "size" : 398458880,
    "cid" : "QmeupdKSRRxijumNzrMmHANErij7YFxvhx1t4GJFXSTQtQ",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 35 */
{
    "fileName" : "debian-9.4.0-i386-DVD-2.iso",
    "size" : 4612392960,
    "cid" : "QmYn6w5qgUnZSKymfMFUiWKuuwVEAuSjtWRH55WCcUkeZy",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 36 */
{
    "fileName" : "debian-9.4.0-i386-DVD-1.iso",
    "size" : 3747086336,
    "cid" : "QmfJSHz9ZuPuFoJG13bs8oRP2rPK3XS9zA7aF9FJe7LKJQ",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 37 */
{
    "fileName" : "debian-9.4.0-i386-DVD-3.iso",
    "size" : 4696012800,
    "cid" : "QmU6FePemByFnEeeR88xfhKcGAhinU9Mp3JbejahB1q5tw",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 38 */
{
    "fileName" : "debian-9.4.0-mips64el-netinst.iso",
    "size" : 288624640,
    "cid" : "QmVEZDkP7c7iSf1hwgKcxpt5kWtGtYC7PWBVmduKbcKZoh",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 39 */
{
    "fileName" : "debian-9.4.0-mips64el-xfce-CD-1.iso",
    "size" : 677339136,
    "cid" : "QmYidStmGHPRX5HDSgVQfq9FUQ81Ad8TMHRsTmgxDXkTL6",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 40 */
{
    "fileName" : "debian-9.4.0-mips64el-DVD-1.iso",
    "size" : 4545611776,
    "cid" : "QmeeBMWNm2oZAZs3BXNZC7ENvfjXSgyFBfFuZQNYaB7oRg",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 41 */
{
    "fileName" : "debian-9.4.0-amd64-i386-netinst.iso",
    "size" : 657457152,
    "cid" : "QmRCii3kF37TJ12hkj6fbqEXbUmGW53xzfm18ELXu98xCG",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 42 */
{
    "fileName" : "debian-9.4.0-amd64-netinst.iso",
    "size" : 305135616,
    "cid" : "Qmd349CmqVFpWLzbYjVRrzGu1RhoVcDQiFNSzo9J4Rwny9",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 43 */
{
    "fileName" : "debian-mac-9.4.0-amd64-netinst.iso",
    "size" : 308281344,
    "cid" : "QmRBczypx2WeAxAdPnLTMVpo8dJBaBJSd4mvozZyzLRs9A",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 44 */
{
    "fileName" : "debian-9.4.0-amd64-xfce-CD-1.iso",
    "size" : 677380096,
    "cid" : "QmQyniyJDhdzr8PE7PGskcvQz2CRtMtvBLunGA2bD6DtrN",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 45 */
{
    "fileName" : "debian-9.4.0-amd64-DVD-2.iso",
    "size" : 4697006080,
    "cid" : "QmVvRektvTSzYsmsY5eQv4SPxErWNNqJEnhNMQG21E9Ua2",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 46 */
{
    "fileName" : "debian-9.4.0-amd64-DVD-1.iso",
    "size" : 3977379840,
    "cid" : "QmUnDauMdthtvnhvNwFXLeLi2yEzVRrXSXweXUdiFVCkBx",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 47 */
{
    "fileName" : "debian-9.4.0-amd64-DVD-3.iso",
    "size" : 4691339264,
    "cid" : "QmemvPoa3c6u5FmdvSfjRcjZrvW5PETso1mMeWW7niz58T",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 48 */
{
    "fileName" : "debian-9.4.0-ppc64el-xfce-CD-1.iso",
    "size" : 677009408,
    "cid" : "QmVo3zTCH1tz4tUQs614Dxgz4qo61o1BE7F9CLTXRySXWF",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 49 */
{
    "fileName" : "debian-9.4.0-ppc64el-netinst.iso",
    "size" : 216346624,
    "cid" : "QmNiJ2oTw2C6nqJYWc1gTECPrMHK123KtVmwukfV1VXq8J",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},

/* 50 */
{
    "fileName" : "debian-9.4.0-ppc64el-DVD-1.iso",
    "size" : 4599662592,
    "cid" : "QmcQFAJBd48xSaeWy8xKuEsKnqnv28J8wRvESxS1XZSj93",
    "createdtime" : "2020-09-28T17:44:52.261Z"
},
];

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  mounted() {
  },

  methods: {
    handleAddressChange(value) {
       this.value = value
       this.getTransfers(1, 20)
    },

    getWallet: function() {
      axios.get('/wallet').then((response) => {
        this.walletAddress = response.data
      })
    },
    getTransfers: function(page, pagesize) {
      var conditions = this.buildCondition()
      var u = '/api/wallet/' + this.value + "?page=" + page + "&pageSize=" + pagesize
      if (conditions.length > 0) {
        u += conditions
      }
      axios.get(u).then((response) => {
        this.tableData = response.data.data
        this.pagination.total = response.data.total
      })
    },

    handleTableChange: function(pagination, filters, sorter, { currentDataSource }) {
      this.getTransfers(pagination.current, pagination.pageSize)
      console.log(filters)
      console.log(sorter)
      console.log(currentDataSource)
    },

    formatterTime: function(val) {
      return val ? moment(val*1000).format('YYYY-MM-DD HH:mm:ss') : ''
    },

    formatFilValue: function(val) {
      var fvalue = +(val)
      
      if (Math.abs(fvalue) > Math.pow(10, 15) ) {
        return (fvalue/Math.pow(10,18)).toFixed(5) + " FIL"
      }
      if (Math.abs(fvalue) > Math.pow(10, 6) ) {
        return (fvalue/Math.pow(10,9)).toFixed(5) + " nanoFIL"
      }
      return fvalue.toFixed(5) + " attoFIL"
    },

    buildCondition: function() {
      var qstr = ""
      if (this.search.filtertype.length > 0) {
        qstr += "&type="+this.search.filtertype
      }
      if (this.search.filteroperator.length >0) {
        qstr += "&op=" + this.search.filteroperator + "&value=" + this.search.filtervalue*this.search.filterunit
      }
      return qstr
    },
    submitFilter: function() {
      this.getTransfers(1, 20)
    },
    gotomessage: function(cid) {
       var href="https://filscout.io/zh/pc/message/" + cid
       window.open(href,'_blank')
    },
    fix: function(str) {
      if  (str.length > 10) {
        return str.substring(0, 10)+'****'+str.substring(str.length-10,str.length)
      }
      return str
    },
    onSearch: function(value) {
      console.log(value);
    },
    tosize: function(bytes) {
      if (bytes === 0) return '0 B';
      var k = 1000, // or 1024
        sizes = ['B', 'KB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB'],
        i = Math.floor(Math.log(bytes) / Math.log(k));
      return (bytes / Math.pow(k, i)).toPrecision(3) + ' ' + sizes[i];
    },
    startDown: function(text, record) {
      console.log("start download", text, record)
    },
    showDetails: function(fileName, record) {
        this.detailVisible = true
        this.detailsRecord = record
    },
    handleOk() {
      this.confirmLoading = true;
      setTimeout(() => {
        this.detailVisible = false;
        this.confirmLoading = false;

      }, 1000);
    },
    handleCancel() {
      this.detailVisible = false;
    },
  },
  data: function() {
    return {
      walletAddress:  [],
      value: "",
      columns: columns,
      tableData: tabData,
      detailVisible: false,
      detailsRecord: {},
      confirmLoading: false,
      pagination: {
        total: 20,
        pageSize: 20,
        defaultCurrent: 1,
      },
      search: {
        filtervalue: 0,
        filtertype: "",
        filterunit: 1,
        filteroperator: "",
      },
      filters: {
        typefilter: [
          {
            text: 'NONE',
            value: '',
          },
          {
            text: 'receive',
            value: 'receive',
          }, {
            text: 'send',
            value: 'send',
          }, {
            text: 'burn-fee',
            value: 'burn-fee',
          }, {
            text: 'miner-fee',
            value: 'miner-fee',
          }, {
            text: 'reward',
            value: 'reward',
          }, {
            text: 'burn',
            value: 'burn',
          }, 
        ],
        unitfilters: [
          {text: 'FIL', value: Math.pow(10,18)},
          {text: 'nanoFIL', value: Math.pow(10,9)},
          {text: 'attoFIL', value: 1},
        ],
        operatorfilters: [
          {text: 'NONE', value: ''},
          {text: '=', value: '$eq'},
          {text: '>', value: '$gt'},
          {text: '<', value: '$lt'},
        ],

      },

    }
  },

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.mg20 {
  margin: 5px;
}

.web {
  .ant-modal-content {
    position: relative;
    background-color: #00000000 !important;
    border: 0;
    border-radius: 4px;
    background-clip: padding-box;
    box-shadow: 0 0 0 rgba(0, 0, 0, 0) !important;
  }

  .ant-modal-body {
    padding: 0 !important;
    font-size: 0 !important;
    line-height: 1 !important;
  }
}
</style>
