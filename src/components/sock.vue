<template>
  <div>
  </div>
</template>

<script>
  import Stomp from 'stompjs'
  import { MQ_SERVICE, MQ_USERNAME, MQ_PASSWORD } from '../config/linkparam.js'
  import { getAction } from '../api/manage'
//  import {JeecgListMixin} from '@/mixins/JeecgListMixin'
  export default {
    name: 'entry',
//    mixins: [JeecgListMixin],
    data () {
      return {
        url: {
          list: "/jd/jdZutaiInfo/list",
        },
        client: Stomp.client(MQ_SERVICE)
      }
    },
    created () {
      this.initStart();
      this.connect();
    },

    methods: {
      initStart: function() {
        var _this = this;
        getAction(this.url.list, { pageNo: 1, pageSize: 150 }).then((res) => {
          if (res.success) {
            _this.$root.analogList = res.result.records;
          } else {
            this.dataSource1 = null;
          }
        });
      },
      onConnected: function (frame) {
//        console.log('Connected: ' + frame)
        var destination_pj = "/queue/project_JDYC";
        var destination_aj = "/queue/alarm_JDYC";
        this.client.subscribe(destination_pj, this.responseCallback1, this.onFailed1)
        this.client.subscribe(destination_aj, this.responseCallback2, this.onFailed2)
      },
      onFailed1: function (frame) {
        console.log('Failed 1: ' + frame)
      },
      onFailed2: function (frame) {
        console.log('Failed 2: ' + frame)
      },
      responseCallback1: function (frame) {
        var analogData = eval(frame.body);
        var analog = {};
        analog.id = analogData[0].instance;
        analog.desc = analogData[0].metric;
        analog.value = analogData[0].value;
        analog.timestamp = analogData[0].timestamp;

        let ana = this.$root.analogList.filter(item => item.ztno === analog.id)[0];
        if(ana!=null){
          ana.ztvalue=parseFloat(analog.value).toFixed(2);
        }
        var aa=1;
  /*      console.log(analogList[0].id+" : " + analogList[0].value);
        if(analogList.length>1){
          console.log(analogList[1].id+" : " + analogList[1].value);
        }
*/
      },
      responseCallback2: function (frame) {
        var analogData = eval(frame.body);
        var analog = {};
        analog.id = analogData[0].instance;
        analog.desc = analogData[0].metric;
        analog.value = analogData[0].value;
        analog.timestamp = analogData[0].timestamp;

        let ana = this.$root.analogList.filter(item => item.ztno === analog.id)[0];
        if(ana!=null){
          ana.ztvalue=parseFloat(analog.value).toFixed(2);
        }
      },

      connect: function () {
        var headers = {
          'login': MQ_USERNAME,
          'passcode': MQ_PASSWORD
        }
        this.client.connect(headers, this.onConnected, this.onFailed)
      }
    }
  }
</script>
