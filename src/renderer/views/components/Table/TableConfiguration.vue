<template>
  <div>
    <el-row :gutter="20">
      <el-col :span="4">
        <div class="grid-content"></div>
      </el-col>
      <el-col :span="16">
        <el-form ref="form" :model="form" :label-width="form.labelWidth" center size="mini">
          <el-divider content-position="left">{{ this.$t('common.basic') }}</el-divider>
          <el-form-item :label="this.stringFormat('{0}{1}', [this.$t('common.table'), this.$t('common.engine')])">
            <el-tag size="mini">{{ form.engine }}</el-tag>
          </el-form-item>
          <el-form-item :label="this.stringFormat('{0}{1}', [this.$t('common.table'), this.$t('common.name')])">
            <el-input v-model="form.name"/>
          </el-form-item>
          <el-divider content-position="left">{{ this.$t('common.column') }}</el-divider>
          <el-row :gutter="20">
            <el-form-item v-for="(column) in form.columns" :key="column.key">
              <template slot="label">
                <el-tooltip :content="$t('common.delete')" placement="top">
                  <el-button type="danger" circle @click.prevent="handlerRemoveColumn(column)">
                    <i class="fa fa-trash"></i>
                  </el-button>
                </el-tooltip>
              </template>
              <el-col :span="6">
                <el-input v-model="column.name"
                          :placeholder="stringFormat('{0}{1}', [$t('common.column'), $t('common.name')])"/>
              </el-col>
              <el-col :span="6">
                <el-select v-model="column.type"
                           :placeholder="stringFormat('{0}{1}', [$t('common.column'), $t('common.type')])">
                  <el-option v-for="item in ColumnTypeUtils.TYPES" :key="item" :label="item" :value="item"/>
                </el-select>
              </el-col>
              <el-col :span="6">
                <el-input v-model="column.comment"
                          :placeholder="stringFormat('{0}{1}', [$t('common.column'), $t('common.comment')])"/>
              </el-col>
              <!--              <el-col :span="6">-->
              <!--                <el-tooltip :content="$t('tooltip.is_empty')" placement="top">-->
              <!--                  <el-switch v-model="column.empty" :active-text="$t('common.yes')" :inactive-text="$t('common.no')"/>-->
              <!--                </el-tooltip>-->
              <!--              </el-col>-->
            </el-form-item>
          </el-row>
          <el-form-item>
            <el-button @click="handlerAddColumn">
              {{ this.stringFormat('{0}{1}', [this.$t('common.add'), this.$t('common.column')]) }}
            </el-button>
          </el-form-item>
          <el-divider content-position="left">{{ this.$t('common.property') }}</el-divider>
          <table-engine-kafka v-if="form.engine === 'Kafka'"
                              @change="handlerTableEngineConfiguration($event)"></table-engine-kafka>
          <table-engine-hdfs v-if="form.engine === 'HDFS'"
                             @change="handlerTableEngineConfiguration($event)"></table-engine-hdfs>
        </el-form>
      </el-col>
      <el-col :span="4">
        <div class="grid-content"></div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import TableEngineKafka from './Engines/Kafka'
import TableEngineHdfs from './Engines/HDFS'

export default {
  name: 'TableConfiguration',
  components: {
    TableEngineKafka,
    TableEngineHdfs
  },
  props: {
    engine: {
      type: String,
      default: null
    }
  },
  created() {
    this.form.engine = this.engine
  },
  data() {
    return {
      form: {
        labelWidth: '120px',
        name: null,
        engine: null,
        columns: []
      }
    }
  },
  methods: {
    handlerAddColumn() {
      this.form.columns.push({
        name: '',
        type: 'String',
        comment: '',
        empty: false,
        key: Date.now()
      })
    },
    handlerRemoveColumn(item) {
      const index = this.form.columns.indexOf(item)
      if (index !== -1) {
        this.form.columns.splice(index, 1)
      }
    },
    closeDialog() {
      this.$emit('close')
    },
    handlerChange() {
      this.$emit('getValue', this.form.columns)
    },
    handlerTableEngineConfiguration(event) {
      this.form.property = event
    }
  },
  watch: {
    form: {
      deep: true,
      handler() {
        this.$emit('change', this.form)
      }
    }
  }
}
</script>

<style>
.grid-content {
  border-radius: 4px;
  min-height: 36px;
}
</style>
