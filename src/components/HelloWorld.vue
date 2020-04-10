<template>
  <div>
    <el-button @click="resetDateFilter">清除日期过滤器</el-button>
    <el-button @click="clearFilter">清除所有过滤器</el-button>
    <el-table
      ref="filterTable"
      :data="tableData"
      style="width: 100%">
      <el-table-column
        prop="date"
        label="日期"
        sortable
        width="180"
        :filters="[{text: '2016-05-01', value: '2016-05-01'}, {text: '2016-05-02', value: '2016-05-02'}, {text: '2016-05-03', value: '2016-05-03'}, {text: '2016-05-04', value: '2016-05-04'}]"
        :filter-method="filterHandler"
      >
      </el-table-column>
      <el-table-column
        prop="name"
        label="姓名"
        width="180">
      </el-table-column>
      <el-table-column
        prop="address"
        label="地址"
        :formatter="formatter">
      </el-table-column>
      <el-table-column
        prop="tag"
        label="标签"
        width="100"
        :filters="[{ text: '家', value: '家' }, { text: '公司', value: '公司' }]"
        :filter-method="filterTag"
        filter-placement="bottom-end">
        <template slot-scope="scope">
          <el-tag
            :type="scope.row.tag === '家' ? 'primary' : 'success'"
            disable-transitions>{{scope.row.tag}}
          </el-tag>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: 'HelloWorld',
    data () {
      return {
        tableData: [{
          date: '2016-05-02',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄',
          tag: '家'
        }, {
          date: '2016-05-02',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1517 弄',
          tag: '公司'
        }, {
          date: '2016-05-01',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1519 弄',
          tag: '家'
        }, {
          date: '2016-05-03',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1516 弄',
          tag: '公司'
        }]
      }
    },
    methods: {
      resetDateFilter () {
        this.$refs.filterTable.clearFilter('date')
      },
      clearFilter () {
        this.$refs.filterTable.clearFilter()
      },
      formatter (row, column) {
        return row.address
      },
      filterTag (value, row) {
        return row.tag === value
      },
      filterHandler (value, row, column) {
        const property = column['property']
        return row[property] === value
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only


 # -*- coding:utf-8 -*-
import json
import time

import pandas
import requests
from bs4 import BeautifulSoup
from flask import Flask, render_template, g, Response

app = Flask(__name__)
app.config['JSON_AS_ASCII'] = False
app.config.update(RESTFUL_JSON=dict(ensure_ascii=False))
ctx = app.app_context()
ctx.push()


# items=[]
# headers = {'Cookie':'__jda=122270672.15834651715451067097227.1583465172.1583465172.1583465172.1; __jdc=122270672; __jdv=122270672|direct|-|none|-|1583465171546; 3AB9D23F7A4B3C9B=JKGMBDOFWDUPGUJAIGNOICCJIQYPDWLPYQYVBJPCJFOT5CJAMW2NTQHRXLSUWDGEH66T6H6BB5XCZTE4JIJIWWQOCY; __jdu=15834651715451067097227; wlfstk_smdl=t4rfbh1rj4asn3l4kv1yui8vuwdxhvob; TrackID=1knN7uErZGB7-36enL8xnA4cytDQcNShwxTz9Xf46lo7ndF6aN8xRYSB5x_vb8B_orivOfL3g18Yox2yXxQziIDLoN-FbsHfMk0dRVUL20yAi_fdTQ_8KTmqS__Lbi_Nq; pinId=4AzYpL5oOAlzzZlqIIaK1A; pin=18627783779_p; unick=imc700; ceshi3.com=201; _tp=N%2FWw6vyqMUG740PqQGV%2F3g%3D%3D; _pst=18627783779_p; xtest=2290.cf6b6759; shshshfpb=bG2CsP4nrxOixu0q9acC4hw%3D%3D; shshshfpa=e44502bb-9d30-c9e8-571a-3b045fd609d3-1573647078; qrsc=1; areaId=19; ipLoc-djd=19-1607-3155-0; user-key=6ce4940a-c4a7-4fe5-8bfd-683e73ed30b0; cn=0; shshshfp=5bd7fbbc11524c582157a6eb71ea65af; thor=D3B7A4E104301A8394CA2F828DCEDA7B71FB89F2FCDA1D723BC6CBCF20F97835C2EDDDC2B8D8C47D47F2F10887FE21C64078732801959AA9DCA4951E104946BCC1FB6ADCEE0143EE12DAD5B4848AEA5088B8CE4495FE2113DF9275B1332A0D30299D6317154A54BC827664EC4A7BB501C6C243A858C0365DD865CDFDB5248D54C49005FC1283629E6C79D6532CE55A86; __jdb=122270672.5.15834651715451067097227|1.1583465172; shshshsID=332df5aa666e8a1e97cf93733450fc9a_3_1583465450983'
#            ,'Upgrade-Insecure-Requests':'1'
#            ,'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.117 Safari/537.36'
#            }
#
# itemUrls=[]
# def getReq(url):
#     print(url)
#     items.clear()
#     target = url
#     r = requests.get(url=target, headers=headers)
#     r.encoding = "utf-8"
#     htmlCode = r.text
#     print(htmlCode)
#     doc = BeautifulSoup(htmlCode, "lxml")
#     items.extend(doc.find_all("li", {"class": "gl-item"}))
#     # print(items[0])
#     # print(len(items))
#     for item in items:
#         find_all = item.find_all("i", {"class": "goods-icons4 J-picon-tips"})
#         if len(find_all)>0:
#             if "促销" in str(find_all[0].get("data-tips")):
#                 find_alla = item.find_all("a")
#                 if str(find_alla[0].get("href")) not in itemUrls:
#                     print(item.find_all("em")[-1].text,"                                                            ",item.find_all("strong")[0].text,"                                                            ",find_all[0].text,"                                                            ",str(find_alla[0].get("href")))
#                     itemUrls.append(str(find_alla[0].get("href")))
#     time.sleep(2)


@app.route('/ye')
def ye():
    data = pandas.read_excel("/Users/hanxu/Downloads/testVue.xlsx")
    row_list = []
    for row in data.iterrows():
        row_data = {}
        for i in range(0, len(row[1])):
            row_data['col' + str(i)] = str(row[1][i])
        row_list.append(row_data)
    print(row_list)
    data = json.dumps(row_list, indent=4)
    # response = Response(data, mimetype='application/json')
    return data



if __name__ == '__main__':
    app.run(host='192.168.2.123',port=5001, debug=True)







 -->
<style scoped>

</style>
