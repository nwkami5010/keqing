<template>
  <Layout class-prefix="layout">

    <NumberPad :value.sync="record.amount" @submit="saveRecord" />
    <Types :value.sync="record.type" />
    <Notes @update:value="onUpdateNotes"/>
    <Tags :data-source.sync="tags" @update:value="onUpdateTags"/>
  </Layout>
</template>

<script lang="ts">
  import Vue from 'vue'
  import NumberPad from '@/components/Money/NumberPad.vue'
  import Types from '@/components/Money/Types.vue'
  import Notes from '@/components/Money/Notes.vue'
  import Tags from '@/components/Money/Tags.vue'
  import {Component, Watch} from 'vue-property-decorator';
  import model from '@/model';

  const recordList = model.fetch()

  @Component({
    components: {Tags, Notes, Types, NumberPad},
  })
  export default class Money extends Vue {
    tags = ['衣', '食', '住', '行', '彩票'];
    recordList: RecordItem[] = JSON.parse(window.localStorage.getItem('recordList') ||'[]');//recordList的类型是record数组
    record: RecordItem = {tags: [],notes: '',type:'-',amount: 0
    };

    onUpdateTags(value: string[]){
      this.record.tags = value
    }
    onUpdateNotes(value: string){
      this.record.notes = value
    }
    /*onUpdateType(value: string){
      this.record.type = value
    }*/
    saveRecord(){
      const deepClone: RecordItem = model.clone(this.record);
      deepClone.createdAt = new Date();
      this.recordList.push(deepClone);
      console.log(this.recordList)
     // localStorage.set('recordList',JSON.stringify(this.recordList));
    }
    @Watch('recordList')
    onRecordListChange(){
      model.save(this.recordList);
    }
  }
</script>

<style lang="scss">
  .layout-content {
    display: flex;
    flex-direction: column-reverse;
  }
</style>

