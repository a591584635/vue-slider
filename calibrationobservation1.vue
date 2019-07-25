<template>
  <div id="calibra">
    <!-- <el-table
    :data="tableData"
    border
    style="width: 100%">
      <el-table-column
        prop="date"
        label="分数"
        width="180">
      </el-table-column>
      <el-table-column
        prop="name"
        label="得分率区间"
        width="180">
      </el-table-column>
      <el-table-column
        prop="address"
        label="一致样本数">
      </el-table-column>
       <el-table-column
        prop="address"
        label="抽样偏高">
      </el-table-column>
       <el-table-column
        prop="address"
        label="抽样偏低">
      </el-table-column>
    </el-table> -->
    <table border="1" cellspacing="0" style="color:#000;width:100%;height:200px">
      <tr>
        <th>分数</th>
        <th>得分率区间</th>
        <th>一致样本数</th>
        <th>抽样偏高</th>
        <th>抽样偏低</th>
      </tr>
      <tr>
        <td>0.0</td>
        <td>0.0-{{hugeValues[0]}}</td>
        <td>54/80</td>
        <td>10/80</td>
        <td>16/80</td>
      </tr>
      <tr>
        <td>0.5</td>
        <td>{{hugeValues[0]}}-{{hugeValues[1]}}</td>
        <td>22/83</td>
        <td>33/83</td>
        <td>28/83</td>
      </tr>
      <tr>
        <td>1.0</td>
        <td>{{hugeValues[1]}}-{{hugeValues[2]}}</td>
        <td>66/90</td>
        <td>0/90</td>
        <td>24/90</td>
      </tr>
      <tr>
        <td>1.5</td>
        <td>{{hugeValues[2]}}-100.0</td>
        <td>73/90</td>
        <td>6/90</td>
        <td>11/90</td>
      </tr>
    </table>
    <Dcalibration class="linetop" :min="0" :max="1.5" :step=0.5 labels></Dcalibration>
    <Dcalibration class="huge" :min="0" :max="100" :step=1 :values="hugeValues"  floats></Dcalibration>
  </div>
</template>
<script>
import Dcalibration from "./calibrationobservation.vue";
export default {
  data() {
    return {
      hugeValues: [30, 50, 80]
    };
  },
  components: {
    Dcalibration
  }
};
</script>
<style scoped>
/deep/ .linetop .slider-pips__range  {
  display: none;
}
</style>

  
 
