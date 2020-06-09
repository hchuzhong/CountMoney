<template>
  <div>
    <Layout class-prefix="layout">
      <NumberPad @update:value="onUpdateAmount" @submit="saveRecord"/>
      <Tabs :data-source="typeList"
            :value.sync="record.type"/>
      <div class="notes">
        <FormItem field-name="备注"
                  placeholder="在这里输入备注"
                  :value.sync="record.notes"/>
      </div>
      <Tags @update:value="record.tags = $event"/>
    </Layout>
  </div>
</template>

<script lang="ts">
  import NumberPad from '@/components/Money/NumberPad.vue';
  import FormItem from '@/components/Money/FormItem.vue';
  import Tags from '@/components/Money/Tags.vue';
  import Vue from 'vue';
  import {Component} from 'vue-property-decorator';
  import Tabs from '@/components/Tabs.vue';
  import typeList from '@/constants/typeList';

  @Component({
    components: {Tabs, Tags, FormItem, NumberPad},
  })
  export default class Money extends Vue {
    record: RecordItem = {
      tags: [], notes: '', type: '-', amount: 0
    };

    typeList = typeList;

    get recordList() {
      return this.$store.state.recordList;
    }

    created() {
      this.$store.commit('fetchRecords');
    }

    onUpdateNotes(value: string) {
      this.record.notes = value;
    }

    onUpdateAmount(value: string) {
      this.record.amount = parseFloat(value);
    }

    saveRecord() {
      if (!this.record.tags || this.record.tags.length === 0) {
        window.alert('请选择至少一个标签');
        this.$store.state.chooseTag = false;
      } else{
        this.$store.commit('createRecord', this.record);
        if (this.$store.state.createRecordError === null) {
          window.alert('已保存');
          this.record.notes = '';
        }
        this.$store.state.chooseTag = true;
      }
    }

  }
</script>

<style lang="scss" scoped>
  ::v-deep {
    .layout-content {
      display: flex;
      flex-direction: column-reverse;
    }

    .notes {
      padding: 10px 5px;
      input{
        height: 20px;
      }
    }
  }
</style>