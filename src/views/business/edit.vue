<template>
  <div class="generate">
    <el-form ref="setForm" label-suffix="：" :model="setData" :rules="rules" size="small" label-width="180px">
      <div class="base">
        <div class="base-header">项目配置</div>
        <div class="base-content">
          <el-form-item label="所属项目" prop="project">
            <el-input v-model="setData.project" placeholder="请输入项目名称" clearable></el-input>
          </el-form-item>
        </div>
      </div>

      <div class="base">
        <div class="base-header">服务配置</div>

        <div class="base-content">
          <el-form-item label="服务名称" prop="name">
            <el-input v-model="setData.name" placeholder="请输入服务名称" clearable></el-input>
          </el-form-item>
          <el-form-item label="服务Code" prop="code">
            <el-input v-model="setData.code" placeholder="请输入服务Code" clearable></el-input>
          </el-form-item>
          <el-form-item label="服务描述" prop="desc">
            <el-input v-model="setData.desc" type="textarea" placeholder="请输入服务描述" :autosize="{minRows: 4, maxRows: 4}"></el-input>
          </el-form-item>
          <el-form-item label="服务图标" prop="icon">
            <cloud-icon @selectIcon="selectIcon" :icon-name="setData.icon" />
          </el-form-item>
          <el-form-item label="服务展现类型" prop="type">
            <el-radio-group v-model="setData.type" size="small">
              <el-radio v-for="(item, index) in typeOptions" :key="index" :label="item.value" :disabled="item.disabled">{{item.label}}</el-radio>
            </el-radio-group>
          </el-form-item>
        </div>

      </div>

      <div class="base" v-show="setData.type == 'list'">
        <div class="base-header">表格配置</div>

        <div class="base-content">
          <el-form-item label="是否支持表格配置" prop="tableConfig.setting">
            <el-switch v-model="setData.tableConfig.setting"></el-switch>
            <el-popover placement="right" trigger="hover" popper-class="reminder">
              <span>表格配置：表格右上角操作按钮，包含<span class="high">数据刷新</span>、<span class="high">下载Excel</span>、<span class="high">自定义列</span></span>
              <el-button class="reminderBtn" type="text" slot="reference" icon="el-icon-warning-outline"></el-button>
            </el-popover>
          </el-form-item>
          <el-form-item label="表格查询入参结构" prop="tableConfig.isPublic">
            <el-switch v-model="setData.tableConfig.isPublic"></el-switch>
            <el-popover placement="right" trigger="hover" popper-class="reminder">
              <span>表格查询入参结构默认是：<span class="high">组装searchDTOList结构，资源管理接口专用</span></span>
              <el-button class="reminderBtn" type="text" slot="reference" icon="el-icon-warning-outline"></el-button>
            </el-popover>
            <div class="example">
              <div class="example-header">示例：</div>
              <div class="example-struct" v-if="setData.tableConfig.isPublic">
                <div class="example-struct-desc">Url: /fitmgr-publiccloud/v1/Huaweicloud/image/image</div>
                <div>入参结构：{{JSON.stringify(structExample2, null, 2)}}</div>
              </div>
              <div class="example-struct" v-else>
                <div class="example-struct-desc">Url: /resource/cmdb/page</div>
                <div>入参结构：{{JSON.stringify(structExample1, null, 2)}}</div>
              </div>
            </div>
          </el-form-item>
          <el-form-item label="是否表格分页" prop="tableConfig.pagination">
            <el-switch v-model="setData.tableConfig.pagination"></el-switch>
            <el-popover placement="right" trigger="hover" popper-class="reminder">
              <span>表格分页：表格是否支持后端分页展示数据</span>
              <el-button class="reminderBtn" type="text" slot="reference" icon="el-icon-warning-outline"></el-button>
            </el-popover>
          </el-form-item>
          <el-form-item v-if="setData.tableConfig.pagination" label="默认一页显示多少条" prop="tableConfig.pageSize">
            <el-select v-model="setData.tableConfig.pageSize">
              <el-option v-for="item in pageSizesOptions" :key="item.value" :label="item.label" :value="item.value"></el-option>
            </el-select>
            <el-popover placement="right" trigger="hover" popper-class="reminder">
              <span>表格初始化出来时：默认基于后端分页限制，一页展示<span class="high">{{setData.tableConfig.pageSize}}条</span>数据</span>
              <el-button class="reminderBtn" type="text" slot="reference" icon="el-icon-warning-outline"></el-button>
            </el-popover>
          </el-form-item>

          <el-form-item label="表格类型" prop="tableConfig.type">
            <el-radio-group v-model="setData.tableConfig.type">
              <el-radio :label="null">普通表格</el-radio>
              <el-radio label="radio">单选表格</el-radio>
              <el-radio label="selection">多选表格</el-radio>
            </el-radio-group>
          </el-form-item>

          <el-form-item label="表格列配置" label-width="110px" class="tableConfig" prop="tableColumns">

            <div class="columns">
              <!-- setData.tableColumns -->
              <div class="columns-item">
                <div class="columns-item-node">
                  <div class="header">列对应属性</div>
                </div>
                <div class="columns-item-node">
                  <div class="header">表头名称</div>
                </div>
                <div class="columns-item-node miniWidth">
                  <div class="header">列宽度(px)</div>
                </div>
                <div class="columns-item-node miniWidth">
                  <div class="header">是否开启Tooltip</div>
                </div>
                <div class="columns-item-node miniWidth">
                  <div class="header">设置为详情入口</div>
                </div>
                <div class="columns-item-node miniWidth">
                  <div class="header" v-if="isConfigInfo">详情入口回调方法名</div>
                </div>
                <div class="columns-item-node miniWidth">
                  <div class="header">设置为操作列</div>
                </div>
                <div class="columns-item-node miniWidth">
                  <div class="header" v-if="isConfigOperate">操作列配置</div>
                </div>
                <div class="columns-item-node miniWidth">
                  <div class="header">设置为枚举列</div>
                </div>
                <div class="columns-item-node miniWidth">
                  <div class="header" v-if="isConfigEnum">枚举列配置</div>
                </div>
              </div>
              <div class="columns-item" v-for="(item,index) in setData.tableColumns" :key="index">
                <div class="columns-item-node">
                  <el-input placeholder="列对应属性" v-model="item.prop"></el-input>
                </div>
                <div class="columns-item-node">
                  <el-input placeholder="表头名称" v-model="item.label"></el-input>
                </div>
                <div class="columns-item-node miniWidth">
                  <el-input placeholder="列宽度(px)" v-model="item.width"></el-input>
                </div>
                <div class="columns-item-node miniWidth">
                  <el-switch v-model="item['show-overflow-tooltip']"></el-switch>
                </div>
                <div class="columns-item-node miniWidth">
                  <el-switch v-model="item.isInfo"></el-switch>
                </div>
                <div :class="[{'placeHidden':!item.isInfo,'placeVisible':item.isInfo},'columns-item-node miniWidth']" v-if="isConfigInfo">
                  <el-input placeholder="方法名" v-model="item.funName"></el-input>
                </div>
                <div class="columns-item-node miniWidth">
                  <el-switch v-model="item.isOperate"></el-switch>
                </div>
                <div :class="[{'placeHidden':!item.isOperate,'placeVisible':item.isOperate},'columns-item-node miniWidth']" v-if="isConfigOperate">
                  <el-button size="mini" icon="el-icon-setting" @click="setOperateColumn(item,index)">{{item.operateFun && item.operateFun.length?"查看操作列":"设置操作列"}}</el-button>
                </div>

                <div class="columns-item-node miniWidth">
                  <el-switch v-model="item.isEnum" @change="changeTableEnumType(item)"></el-switch>
                </div>
                <div :class="[{'placeHidden':!item.isEnum,'placeVisible':item.isEnum},'columns-item-node miniWidth']" v-if="isConfigEnum">
                  <el-button size="mini" icon="el-icon-setting" @click="setEnumColumn(item,index)">{{item.enums ?"查看枚举列":"设置枚举列"}}</el-button>
                </div>

                <div class="columns-item-operate">
                  <el-button size="mini" icon="el-icon-minus" v-if="setData.tableColumns.length != 1" @click="minus(setData.tableColumns,index)"></el-button>
                  <el-button size="mini" icon="el-icon-plus" @click="plus(setData.tableColumns)" v-if="setData.tableColumns.length == (index + 1)"></el-button>
                </div>
              </div>
            </div>
          </el-form-item>

          <el-form-item label="查询条件配置" label-width="110px" class="tableConfig" prop="searchForm">
            <div class="tableConfig-testData">
              <div class="columns">
                <div class="columns-item">
                  <div class="columns-item-node">
                    <div class="header">条件名称</div>
                  </div>
                  <div class="columns-item-node miniWidth">
                    <div class="header">条件字段</div>
                  </div>
                  <div class="columns-item-node">
                    <div class="header">默认值</div>
                  </div>
                  <div class="columns-item-node">
                    <div class="header">Placeholder</div>
                  </div>
                  <div class="columns-item-node miniWidth">
                    <div class="header">是否隐藏</div>
                  </div>
                  <div class="columns-item-node miniWidth">
                    <div class="header">选择关系</div>
                  </div>
                  <div class="columns-item-node miniWidth">
                    <div class="header">展示类型</div>
                  </div>
                  <div class="columns-item-node miniWidth" v-if="isConfigSelectEnum">
                    <div class="header">下拉框枚举</div>
                  </div>
                </div>
                <div class="columns-item" v-for="(item,index) in setData.searchForm.labels" :key="index">
                  <div class="columns-item-node">
                    <el-input placeholder="输入条件名称" v-model="item.name" clearable></el-input>
                  </div>
                  <div class="columns-item-node miniWidth">
                    <el-select @change="changeSearchField(item)" placeholder="选择字段" v-model="item.field" clearable filterable>
                      <el-option :key="findex" :label="fitem.prop" :value="fitem.prop" v-for="(fitem,findex) in setData.tableColumns"></el-option>
                    </el-select>
                  </div>
                  <div class="columns-item-node">
                    <el-input placeholder="输入默认值" v-model="item.value" clearable></el-input>
                  </div>

                  <div class="columns-item-node">
                    <el-input placeholder="输入Placeholder" v-model="item.placeholder" clearable></el-input>
                  </div>

                  <div class="columns-item-node miniWidth">
                    <el-switch v-model="item.hide"></el-switch>
                  </div>

                  <div class="columns-item-node miniWidth">
                    <el-select placeholder="选择关系" v-model="item.connector" clearable>
                      <el-option label="=" value="0"></el-option>
                      <el-option label="!=" value="1"></el-option>
                      <el-option label=">" value="2"></el-option>
                      <el-option label="<" value="3"></el-option>
                      <el-option label=">=" value="4"></el-option>
                      <el-option label="<=" value="5"></el-option>
                      <el-option label="like" value="6"></el-option>
                      <el-option label="in" value="7"></el-option>
                      <el-option label="is null" value="8"></el-option>
                      <el-option label="is not null" value="9"></el-option>
                    </el-select>
                  </div>
                  <div class="columns-item-node miniWidth">
                    <el-select placeholder="展示类型" v-model="item.type" @change="()=>{changeSearchType(item)}">
                      <el-option label="输入框" value="input"></el-option>
                      <el-option label="下拉框" value="select"></el-option>
                    </el-select>
                  </div>
                  <div :class="[{'placeHidden':item.type != 'select','placeVisible':item.type == 'select'},'columns-item-node miniWidth']" v-if="isConfigSelectEnum">
                    <el-button size="mini" icon="el-icon-setting" @click="setSearchEnumColumn(item,index)">{{item.data && item.data.length ?"查看枚举列":"设置枚举列"}}</el-button>
                  </div>
                  <div class="columns-item-operate">
                    <el-button size="mini" icon="el-icon-minus" v-if="setData.searchForm.labels.length != 1" @click="minus(setData.searchForm.labels,index)"></el-button>
                    <el-button size="mini" icon="el-icon-plus" @click="plus(setData.searchForm.labels)" v-if="setData.searchForm.labels.length == (index + 1)"></el-button>
                  </div>
                </div>
              </div>
            </div>
          </el-form-item>

          <el-form-item label="录入测试数据" label-width="110px" class="tableConfig" prop="testData">
            <div class="tableConfig-testData">
              <div v-for="(item,index) in setData.tableColumns" :key="index" class="tableConfig-testData-item">
                <label>{{item.label}}：</label>
                <el-select v-if="item.enums" clearable placeholder="请选择" v-model="item.value">
                  <el-option :label="obj.label" :value="key" v-for="(obj,key) in item.enums" :key="key"></el-option>
                </el-select>
                <el-input v-else v-model="item.value" :placeholder="`${item.label}测试数据`"></el-input>
              </div>
            </div>
          </el-form-item>

          <el-form-item>
            <el-button type="warning" @click="preview" icon="el-icon-view">表格预览</el-button>
          </el-form-item>
        </div>
      </div>

      <detail ref="detail" :detailConfig="detailConfig" :columns="setData.tableColumns" v-show="setData.type == 'info'" />

      <div class="base-operate">
        <el-button type="primary" @click="submitForm">提交</el-button>
        <el-button @click="resetForm">重置</el-button>
        <el-button @click="$router.go(-1)">返回</el-button>
      </div>

    </el-form>

    <!-- 表格预览 -->
    <el-dialog v-bind="$attrs" custom-class="tablePreview" :close-on-click-modal="false" v-on="$listeners" width="70%" :visible.sync="isPreview" title="预览表格">
      <div class="tablePreview-content">
        <m-table v-if="isPreview" :configs="backupConfigs" />
      </div>

      <div slot="footer">
        <el-button @click="isPreview = false;">关闭</el-button>
      </div>
    </el-dialog>

    <!-- 设置操作列 -->
    <el-dialog v-bind="$attrs" :close-on-click-modal="false" v-on="$listeners" :visible.sync="isOperateColumnConfig" @close="onCloseOperateDialog" title="设置操作列">
      <el-form ref="pageForm" :model="operateConfigFormData" size="small" label-width="180px">
        <el-form-item label="操作列配置" prop="operates" class="tableConfig">
          <div class="columns">
            <div class="columns-item">
              <div class="columns-item-node">
                <div class="header">操作名称</div>
              </div>
              <div class="columns-item-node">
                <div class="header">回调方法名</div>
              </div>
              <div class="columns-item-node miniWidth">
                <div class="header">图标</div>
              </div>
            </div>
            <div class="columns-item" v-for="(item,index) in operateConfigFormData.operates" :key="index">
              <div class="columns-item-node">
                <el-input clearable placeholder="操作名称" v-model="item.label"></el-input>
              </div>
              <div class="columns-item-node">
                <el-input clearable placeholder="回调方法名" v-model="item.function"></el-input>
              </div>
              <div class="columns-item-node miniWidth">
                <el-input clearable placeholder="图标" v-model="item.icon"></el-input>
              </div>

              <div class="columns-item-operate">
                <el-button size="mini" icon="el-icon-minus" v-if="operateConfigFormData.operates.length != 1" @click="minus(operateConfigFormData.operates,index)"></el-button>
                <el-button size="mini" icon="el-icon-plus" @click="plus(operateConfigFormData.operates)" v-if="operateConfigFormData.operates.length == (index + 1)"></el-button>
              </div>
            </div>
          </div>
        </el-form-item>
      </el-form>
      <div slot="footer">
        <el-button @click="isOperateColumnConfig = false;">取消</el-button>
        <el-button type="primary" @click="saveOperateConfig">确定</el-button>
      </div>
    </el-dialog>

    <!-- 设置枚举列 -->
    <el-dialog v-bind="$attrs" :close-on-click-modal="false" v-on="$listeners" :visible.sync="isEnumColumn" @close="onCloseEnumDialog" title="设置枚举列">
      <el-form ref="pageForm" :model="enumConfigFormData" size="small" label-width="120px">
        <el-form-item label="枚举列配置" prop="operates" class="tableConfig">
          <div class="columns">
            <div class="columns-item">
              <div class="columns-item-node">
                <div class="header">枚举Value</div>
              </div>
              <div class="columns-item-node">
                <div class="header">枚举显示Label</div>
              </div>
              <div class="columns-item-node miniWidth">
                <div class="header">枚举呈现组件</div>
              </div>
              <div class="columns-item-node miniWidth">
                <div class="header">组件类型</div>
              </div>
            </div>

            <div class="columns-item" v-for="(item,index) in enumConfigFormData.arrEnums" :key="index">
              <div class="columns-item-node">
                <el-input clearable placeholder="枚举Value" v-model="item.key"></el-input>
              </div>
              <div class="columns-item-node">
                <el-input clearable placeholder="枚举显示Label" v-model="item.label"></el-input>
              </div>

              <div class="columns-item-node miniWidth">
                <el-select clearable placeholder="请选择枚举呈现组件" v-model="item.component">
                  <el-option label="Dot" value="dot"></el-option>
                  <el-option label="El-Tag" value="el-tag"></el-option>
                  <el-option label="Tag" value="tag"></el-option>
                  <el-option label="Level" value="level"></el-option>
                </el-select>
              </div>

              <div class="columns-item-node miniWidth" v-if="item.component">
                <el-select clearable placeholder="请选择组件类型" v-model="item.type">
                  <el-option label="一般" value="primary">
                    <tag type="primary">一般</tag>
                  </el-option>
                  <el-option label="成功" value="success">
                    <tag type="success">成功</tag>
                  </el-option>
                  <el-option label="警告" value="warning">
                    <tag type="warning">警告</tag>
                  </el-option>
                  <el-option label="危险" value="danger">
                    <tag type="danger">危险</tag>
                  </el-option>
                  <el-option label="信息" value="info">
                    <tag type="info">信息</tag>
                  </el-option>
                  <el-option label="测试" value="test">
                    <tag type="test">测试</tag>
                  </el-option>
                </el-select>
              </div>

              <div class="columns-item-operate">
                <el-button size="mini" icon="el-icon-minus" v-if="enumConfigFormData.arrEnums.length != 1" @click="minus(enumConfigFormData.arrEnums,index)"></el-button>
                <el-button size="mini" icon="el-icon-plus" @click="plus(enumConfigFormData.arrEnums)" v-if="enumConfigFormData.arrEnums.length == (index + 1)"></el-button>
              </div>
            </div>
          </div>
        </el-form-item>
      </el-form>
      <div slot="footer">
        <el-button @click="isEnumColumn = false;">取消</el-button>
        <el-button type="primary" @click="saveEnumConfig">确定</el-button>
      </div>
    </el-dialog>

    <!-- 设置查询枚举列 -->
    <el-dialog v-bind="$attrs" :close-on-click-modal="false" v-on="$listeners" :visible.sync="isSearchEnumColumn" @close="onCloseSearchEnumDialog" title="设置枚举列">
      <el-form ref="pageForm" :model="enumConfigSearchData" size="small" label-width="120px">
        <el-form-item label="枚举列配置" prop="operates" class="tableConfig">
          <div class="columns">
            <div class="columns-item">
              <div class="columns-item-node">
                <div class="header">枚举Value</div>
              </div>
              <div class="columns-item-node">
                <div class="header">枚举显示Label</div>
              </div>
              <div class="columns-item-node miniWidth">
                <div class="header">枚举呈现组件</div>
              </div>
              <div class="columns-item-node miniWidth">
                <div class="header">组件类型</div>
              </div>
            </div>

            <div class="columns-item" v-for="(item,index) in enumConfigSearchData.arrEnums" :key="index">
              <div class="columns-item-node">
                <el-input clearable placeholder="枚举Value" v-model="item.value"></el-input>
              </div>
              <div class="columns-item-node">
                <el-input clearable placeholder="枚举显示Label" v-model="item.label"></el-input>
              </div>

              <div class="columns-item-node miniWidth">
                <el-select clearable placeholder="请选择枚举呈现组件" v-model="item.component">
                  <el-option label="Dot" value="dot"></el-option>
                  <el-option label="El-Tag" value="el-tag"></el-option>
                  <el-option label="Tag" value="tag"></el-option>
                  <el-option label="Level" value="level"></el-option>
                </el-select>
              </div>

              <div class="columns-item-node miniWidth" v-if="item.component">
                <el-select clearable placeholder="请选择组件类型" v-model="item.type">
                  <el-option label="一般" value="primary">
                    <tag type="primary">一般</tag>
                  </el-option>
                  <el-option label="成功" value="success">
                    <tag type="success">成功</tag>
                  </el-option>
                  <el-option label="警告" value="warning">
                    <tag type="warning">警告</tag>
                  </el-option>
                  <el-option label="危险" value="danger">
                    <tag type="danger">危险</tag>
                  </el-option>
                  <el-option label="信息" value="info">
                    <tag type="info">信息</tag>
                  </el-option>
                  <el-option label="测试" value="test">
                    <tag type="test">测试</tag>
                  </el-option>
                </el-select>
              </div>

              <div class="columns-item-operate">
                <el-button size="mini" icon="el-icon-minus" v-if="enumConfigSearchData.arrEnums.length != 1" @click="minus(enumConfigSearchData.arrEnums,index)"></el-button>
                <el-button size="mini" icon="el-icon-plus" @click="plus(enumConfigSearchData.arrEnums)" v-if="enumConfigSearchData.arrEnums.length == (index + 1)"></el-button>
              </div>
            </div>
          </div>
        </el-form-item>
      </el-form>
      <div slot="footer">
        <el-button @click="isSearchEnumColumn = false;">取消</el-button>
        <el-button type="primary" @click="saveSearchEnumConfig">确定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import mTable from "./tableModel";
import detail from "./component/detailConfig";
import cloudIcon from "@/components/CloudIcon/index"
export default {
  components: { mTable, detail, cloudIcon },
  props: [],
  data() {
    return {
      detailConfig: undefined,
      setData: {
        project: "fitmgr",
        name: undefined,
        code: undefined,
        desc: undefined,
        icon: undefined,
        type: "list",
        tableConfig: {
          setting: true,
          pagination: true,
          isPublic: false,//兼容其他类型接口，是否组装searchDTOList结构
          pageSize: 10,
          type: null
        },
        tableColumns: [
          { prop: "cidr", label: "CIDR" },
          { prop: "status", label: "状态", width: "100" },
          { prop: "start_ip", label: "第一个可用地址" },
          { prop: "end_ip", label: "最后一个可用地址" }
        ],
        searchForm: {
          labels: [
            { name: "", field: "", value: "", type: "input", connector: "0", hide: false }
          ],
          btns: [
            {
              name: "查询",
              click: ""
            }
          ],
          inputParams: {}
        }
      },
      operateConfigFormData: {
        operates: [{ function: "", label: "", icon: "" }],

      },
      enumConfigFormData: {
        arrEnums: [{ label: "", type: "", component: "", key: "" }],
      },
      enumConfigSearchData: {
        arrEnums: [{ label: "", type: "", component: "", value: "" }],
      },
      currentRow: null,//当前操作的表格配置列
      rules: {
        project: [{
          required: true,
          message: '请输入所属项目',
          trigger: 'blur'
        }],
        name: [{
          required: true,
          message: '请输入服务名称',
          trigger: 'blur'
        }],
        code: [{
          required: true,
          message: '请输入服务Code',
          trigger: 'blur'
        }],
        desc: [{
          required: true,
          message: '请输入服务描述',
          trigger: 'blur'
        }],
        icon: [{
          required: true,
          message: '请输入服务图标（在云计算图标库中寻找/选择）',
          trigger: 'blur'
        }],
        type: [{
          required: true,
          message: '服务展现类型不能为空',
          trigger: 'change'
        }],
      },
      typeOptions: [{
        "label": "表格",
        "value": "list"
      }, {
        "label": "详情",
        "value": "info"
      }, {
        "label": "关于",
        "value": "about"
      }],
      pageSizesOptions: [
        {
          "label": "5条",
          "value": 5
        }, {
          "label": "10条",
          "value": 10
        }, {
          "label": "20条",
          "value": 20
        }, {
          "label": "30条",
          "value": 30
        }
        , {
          "label": "50条",
          "value": 50
        }
      ],
      structExample1: {
        componentCode: "image",
        current: 1,
        size: 10,
        searchDTOList: [
          {
            key: "status",
            connector: 0,
            value: "active"
          }
        ]
      },
      structExample2: {
        current: 1,
        size: 10,
        status: "active"
      },
      isOperateColumnConfig: false,// 设置操作列弹框
      isEnumColumn: false,// 设置枚举列弹框
      isSearchEnumColumn: false,// 设置查询枚举列弹框
      backupConfigs: {},
      isPreview: false
    }
  },
  computed: {
    isConfigInfo() {
      return this.setData.tableColumns.some(s => s.isInfo)
    },
    isConfigOperate() {
      return this.setData.tableColumns.some(s => s.isOperate)
    },
    isConfigEnum() {
      return this.setData.tableColumns.some(s => s.isEnum)
    },
    isConfigSelectEnum() {
      if (this.setData.searchForm) {
        return this.setData.searchForm.labels.some(s => s.type == 'select')
      }
      else {
        return false;
      }
    }
  },
  watch: {},
  created() {
    this.fetchDetail();
  },
  mounted() { },
  methods: {
    changeTableEnumType(item) {
      if (!item.isEnum) {
        item.enums = undefined;
      }
    },
    changeSearchField(item) {
      item.type = "input";
    },
    changeSearchType(item) {
      let that = this;
      let columns = that.setData.tableColumns.filter(f => f.prop == item.field && f.isEnum)
      if (columns && columns.length) {
        let column = columns[0]
        that.$set(item, "data", this.objToArr(column.enums, "value"));
      }
      else {
        that.$set(item, "data", undefined);
      }
    },
    fetchDetail() {
      this.$axios.post("/server-config/detail", { uuid: this.$route.query.id }).then(res => {
        if (res.status == 200) {
          let source = res.data;
          this.setData.uuid = source.uuid;
          this.setData.project = source.project;
          this.setData.name = source.name;
          this.setData.code = source.code;
          this.setData.desc = source.desc;
          this.setData.icon = source.icon;
          this.setData.tableConfig = JSON.parse(source.tableConfig).tableConfig;
          let tableColumns = JSON.parse(source.tableConfig).tableColumns;
          if (source.searchConfig) {
            this.setData.searchForm = JSON.parse(source.searchConfig)
          }
          // 表格测试数据
          let tableTestData = JSON.parse(source.tableTestData)
          tableTestData.forEach(test => {
            tableColumns.forEach(col => {
              col.value = test[col.prop]
            })
          })
          this.setData.tableColumns = tableColumns
          this.detailConfig = source.detailConfig;
        }
      })
    },
    selectIcon(item) {
      this.setData.icon = item.name;
    },
    submitForm() {
      let that = this;
      this.$refs['setForm'].validate(valid => {
        if (!valid) return
        let tableConfigs = that.dealData();
        let detailConfigs = that.$refs['detail'].setDetailData;
        //组装测试数据
        let detailTestData = {}
        //串联起来所有业务模块测试数据
        detailConfigs.detail.forEach(dc => {
          if (dc.fields && dc.fields.length) {
            dc.fields.forEach(d => {
              let temp = {};
              temp[d.key] = d.value;
              Object.assign(detailTestData, temp);
            })
          }
        })

        let searchConfigStr = undefined;
        if (this.setData.searchForm && this.setData.searchForm.labels.some(s => s.field)) {
          searchConfigStr = JSON.stringify(this.setData.searchForm)
        }


        let params = {
          uuid: this.setData.uuid,
          code: this.setData.code,
          name: this.setData.name,
          project: this.setData.project,
          desc: this.setData.desc,
          icon: this.setData.icon,
          searchConfig: searchConfigStr,
          tableConfig: JSON.stringify(tableConfigs.table),//设置表格配置
          tableBusiness: JSON.stringify(tableConfigs.business),//设置业务配置
          tableTestData: JSON.stringify(tableConfigs.testData),//设置表格配置
          detailConfig: JSON.stringify(detailConfigs.detail),//设置表格配置
          detailTestData: JSON.stringify(detailTestData),//设置表格配置
        }

        this.$axios.post("/server-config/update", params).then(res => {
          if (res.status == 200) {
            this.$message.success(res.msg || "更新服务成功")
          }
          else {
            this.$message.error(res.msg || "更新服务失败")
          }
        })
      })
    },
    resetForm() {
      this.$refs['setForm'].resetFields()
    },
    minus(source, index) {
      source.splice(index, 1);
    },
    plus(source) {
      source.push({ prop: "", label: "" })
    },
    setOperateColumn(row, index) {
      //打开设置表格操作列配置弹框
      this.isOperateColumnConfig = true;
      this.currentRow = row;

      //设置过配置时，回显配置
      if (this.currentRow.operateFun && this.currentRow.operateFun.length) {
        this.operateConfigFormData.operates = this.currentRow.operateFun;
      }
    },
    arrToObj(arr) {
      let target = {};
      if (arr && arr.length) {
        arr.forEach(item => {
          target[item.key] = { label: item.label, type: item.type, component: item.component };
        })
      }
      return target;
    },
    objToArr(obj, fieldName) {
      let target = [];
      for (let key in obj) {
        let item = obj[key];
        let temp = { label: item.label, type: item.type, component: item.component }
        if (fieldName) {
          temp[fieldName] = key;
        }
        else {
          temp["key"] = key;
        }
        target.push(temp)
      }
      return target;
    },
    setEnumColumn(row, index) {
      this.isEnumColumn = true;
      this.currentRow = row;
      if (this.currentRow.enums) {
        // 对象转成数组去弹框中回显
        this.enumConfigFormData.arrEnums = this.objToArr(this.currentRow.enums);
      }
    },
    setSearchEnumColumn(row, index) {
      this.isSearchEnumColumn = true;
      this.currentSearchRow = row;
      if (this.currentSearchRow.data) {
        // 回显枚举
        let data = this.$util.deepCopy(this.currentSearchRow.data);
        this.enumConfigSearchData.arrEnums = data;
      }
    },
    saveOperateConfig() {
      //保存表格操作列配置
      this.currentRow.operateFun = this.$util.deepCopy(this.operateConfigFormData.operates);
      this.isOperateColumnConfig = false;
    },
    saveEnumConfig() {
      //保存表格枚举列配置
      this.currentRow.enums = this.arrToObj(this.$util.deepCopy(this.enumConfigFormData.arrEnums));
      this.isEnumColumn = false;
    },
    saveSearchEnumConfig() {
      //保存表格枚举列配置
      this.currentSearchRow.data = this.$util.deepCopy(this.enumConfigSearchData.arrEnums);
      this.isSearchEnumColumn = false;
    },
    onCloseOperateDialog() {
      //关闭操作列配置弹框回调

      //还原弹框默认值
      this.operateConfigFormData = {
        operates: [{ function: "", label: "", icon: "" }],
      }
    },
    onCloseEnumDialog() {
      //还原弹框默认值
      this.enumConfigFormData = {
        arrEnums: [{ label: "", type: "", component: "", key: "" }],
      }
    },
    onCloseSearchEnumDialog() {
      //还原弹框默认值
      this.enumConfigSearchData = {
        arrEnums: [{ label: "", type: "", component: "", value: "" }],
      }
    },
    preview() {
      this.backupConfigs = this.dealData();
      this.isPreview = !this.isPreview;
    },
    dealData() {
      let backupConfigs = {
        table: {},
        business: {
          list: {
            records: [],
            total: 0
          }
        },
        testData: []
      };
      let backupColumns = this.$util.deepCopy(this.setData.tableColumns);
      let isExist = backupColumns.some(s => s.type == "radio" || s.type == "selection");
      if (isExist) {
        backupColumns = backupColumns.shift();
      }
      // 单选表格
      if (this.setData.tableConfig.type == "radio") {
        backupColumns.unshift({ type: "radio" })
      }
      else if (this.setData.tableConfig.type == "selection") {
        backupColumns.unshift({ type: "selection" })
      }

      let oneTest = {};
      backupColumns.forEach(item => {
        //把当前项设置为详情列
        if (item.isInfo && item.funName) {
          item.operateFun = {
            function: item.funName
          }
        }
        if (item.value) {
          oneTest[item.prop] = item.value;
          delete item.value;
        }
      })

      if (JSON.stringify(oneTest) != "{}") {
        backupConfigs.testData.push(oneTest);
      }

      backupConfigs.table.tableConfig = this.setData.tableConfig;
      backupConfigs.table.searchForm = this.setData.searchForm;
      // 初始化数据
      backupConfigs.business.loading = false;
      backupConfigs.business.inputParams = {
        page: 1,
        limit: 10
      }

      backupConfigs.table.tableColumns = backupColumns;
      return backupConfigs;
    }
  }
}
</script>

<style lang="scss">
.tablePreview {
  &-content {
    max-height: 500px;
    overflow-y: scroll;
  }
}

.generate {
  .tableConfig {
    background: #fafafa;
    padding: 10px;
    overflow-x: scroll;
    &-testData {
      display: flex;
      flex-wrap: wrap;
      &-item {
        display: flex;
        width: 25%;
        margin-bottom: 10px;
        label {
          margin-right: 10px;
          color: #999;
        }
        .el-input {
          width: 200px;
        }
      }
    }

    .columns {
      &-header {
        color: #999;
        display: flex;
        &-item {
          width: 200px;
          margin-right: 10px;
          &.miniWidth {
            width: 120px !important;
          }
        }
      }
      &-item {
        margin-bottom: 10px;
        display: flex;
        &-node {
          border-right: 2px solid #fff;
          .header {
            color: #999;
            width: 190px !important;
          }
          .el-input,
          .el-switch {
            width: 190px !important;
          }
          .el-button {
            width: 160px !important;
          }
          margin-right: 5px;
          padding-right: 5px;
          &.placeHidden {
            visibility: hidden !important;
          }
          &.placeVisible {
            visibility: visible !important;
          }
          &.miniWidth {
            .el-input,
            .el-button,
            .el-switch {
              width: 130px !important;
            }
            .header {
              width: 130px !important;
            }
          }
        }
        &-operate {
          width: 100px;
          display: inherit;
        }
      }
    }
  }

  .example {
    &-header {
      color: #999;
      font-size: 12px;
    }
    &-struct {
      &-desc {
        padding: 5px;
        box-sizing: border-box;
        border: 1px solid #ebebeb;
        border-radius: 3px;
        font-size: 14px;
        line-height: 22px;
        color: #666;
        word-break: break-word;
        margin: 10px 0;
        background-color: #fff;
      }
      line-height: 1.8;
      font-family: Menlo, Monaco, Consolas, Courier, monospace;
      font-size: 12px;
      padding: 18px 24px;
      background-color: #fafafa;
      border: 1px solid #eaeefb;
      margin-bottom: 25px;
      border-radius: 4px;
      -webkit-font-smoothing: auto;
    }
  }
}
</style>


