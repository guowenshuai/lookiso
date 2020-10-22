<template>
  <div class="iso">
  <!-- 搜索 -->
    <div>
      <a-input-search placeholder="Search by keywords or content cid"
          style="width: 30%;"
          size="large"
          class="mg20"
          enter-button @search="onSearch"/> 
    </div>
    <div v-if="downloadStatus.isDownload" class="mg20">
      <span style="width=10%" class="mg20">{{downloadStatus.downRecord.fileName}}</span>
      <a-progress :percent="downloadStatus.percent" status="active" style="width:60%" class="mg20"/>
      <a-button @click="abortDown()" style="width:10%" class="mg20">Abort</a-button>
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

export default {
  name: 'ISOLIST',
  props: {
    msg: String
  },
  mounted() {
    this.getData(1, 10)
  },

  methods: {
    getData: function(page, pagesize) {
      let p = page ? page : 1
      let ps = pagesize ? pagesize : 10
      var u = '/api/v1/ipfs/iso' + "?page=" + p + "&pageSize=" + ps
      axios.get(u).then((response) => {
        this.tableData = response.data.data.results
        this.pagination.total = response.data.data.count
      })
    },

    handleTableChange: function(pagination, filters, sorter, { currentDataSource }) {
      this.getData(pagination.current, pagination.pageSize)
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

    fix: function(str) {
      if  (str.length > 10) {
        return str.substring(0, 10)+'****'+str.substring(str.length-10,str.length)
      }
      return str
    },
    onSearch: function(value) {
      if (value.length === 0) {
        return this.getData()
      }
      var u = '/api/v1/ipfs/iso?cid=' + value
      axios.get(u).then((response) => {
        this.tableData = response.data.data.results.splice(0,1)        
        if (response.data.data.results) {
          if (response.data.data.results.length > 1) {
            this.tableData = response.data.data.results.splice(0,1)
          } else {
            this.tableData = response.data.data.results
          }
        } else {
           this.tableData = []
        }
        this.pagination.total = 1
      }).catch(() => {
        this.tableData = []
        this.pagination.total = 0
      })
    },
    tosize: function(bytes) {
      if (bytes === 0) return '0 B';
      var k = 1000, // or 1024
        sizes = ['B', 'KB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB'],
        i = Math.floor(Math.log(bytes) / Math.log(k));
      return (bytes / Math.pow(k, i)).toPrecision(3) + ' ' + sizes[i];
    },
    startDown: function(text, record) {
      if (this.downloadStatus.isDownload) {
        alert(`waiting finish`);
        return
      }
      console.log("start download", text, record)
      this.download("http://61.155.145.10:8080/ipfs/" + text.cid, text)
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
        this.startDown(this.detailsRecord, this.detailsRecord)

      }, 1000);
    },
    handleCancel() {
      this.detailVisible = false;
    },

    // 流式下载
    getBlob(url,cb) {
            var xhr = new XMLHttpRequest()
            let that = this
            this.downloadStatus.xhr = xhr
            xhr.open('GET', url, true);
            xhr.responseType = 'blob';
            xhr.onload = function() {
              if (xhr.status === 200) {
                  cb(xhr.response);
              }
            };
            xhr.onerror = () => { // only triggers if the request couldn't be made at all
              alert(`Network Error`);
              this.downloadStateRest()
            };
            xhr.onprogress = (event) => { // triggers periodically
              // event.loaded - how many bytes downloaded
              // event.lengthComputable = true if the server sent Content-Length header
              // event.total - total number of bytes (if lengthComputable)
              console.log(`Received ${event.loaded} of ${event.total}`);
              that.downloadStatus.percent = Math.floor((event.loaded/event.total) * 100)
            };
            xhr.send();
            // xhr.abort();
    },
    abortDown() {
      this.downloadStatus.xhr.abort()
      this.downloadStateRest()
    },

    /**
    * 保存
    * @param  {Blob} blob     
    * @param  {String} filename 想要保存的文件名称
    */
    saveAs(blob, filename) {
        if (window.navigator.msSaveOrOpenBlob) {
                navigator.msSaveBlob(blob, filename);
        } else {
                var link = document.createElement('a');
                var body = document.querySelector('body');

                link.href = window.URL.createObjectURL(blob);
                link.download = filename;

                // fix Firefox
                link.style.display = 'none';
                body.appendChild(link);

                link.click();
                body.removeChild(link);

                window.URL.revokeObjectURL(link.href);
        }
    },

    /**
    * 下载
    * @param  {String} url 目标文件地址
    * @param  {String} filename 想要保存的文件名称
    */
    download(url, record) {
        this.downloadStatus.downRecord = record
        this.downloadStatus.isDownload = true
        this.downloadStatus.percent = 0

        this.getBlob(url, (blob) => {
            this.saveAs(blob, record.fileName);
            this.downloadStateRest()
        });
    },
    downloadStateRest() {
      this.downloadStatus.downRecord = {}
      this.downloadStatus.isDownload = false
      this.downloadStatus.percent = 0      
    }
    
  },
  data: function() {
    return {
      value: "",
      columns: columns,
      tableData: [],
      detailVisible: false,
      detailsRecord: {},
      confirmLoading: false,
      pagination: {
        total: 20,
        pageSize: 10,
        defaultCurrent: 1,
      },
      downloadStatus: {
        xhr: {},
        isDownload: false,
        downRecord: {},
        percent: 0,
      }
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
