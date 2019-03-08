<template>
    <div :class="{ isDetail }" class="container-warp" style="margin-top:0;">
        <div :class="{ detailLeft: isDetail }">
            <div v-if="isDetail" style="margin-bottom: 10px;">
                <el-button plain class="primaryBtn resetPadding" @click="$router.push({ name: 'InternalEdit', params: { id: $route.params.id } })">编辑</el-button>
                <el-button plain class="primaryBtn resetPadding" @click="$emit('closeNewTab')">取消</el-button>
            </div>
            <div class="primaryFrom">
                <el-form ref="form" :model="form" :rules="rules" label-width="100px">
                    <div>
                        <div class="displayStyle">
                            <el-form-item label="请求方法:" prop="requestMethod">
                                <el-select v-model="form.requestMethod" :disabled="isDetail" placeholder="请选择请求方法" size="small" class="row_width">
                                    <el-option v-for="item in requestMethodOptions" :key="item.value" :label="item.label" :value="item.value"></el-option>
                                </el-select>
                            </el-form-item>
                        </div>
                        <div class="displayStyle">
                            <el-form-item label="数据类型:" prop="dataType">
                                <el-select v-model="form.dataType" :disabled="isDetail" placeholder="请选择数据类型" size="small" class="row_width">
                                    <el-option v-for="item in dataTypeOptions" :key="item.value" :label="item.label" :value="item.value"></el-option>
                                </el-select>
                            </el-form-item>
                        </div>
                        <div class="displayStyle">
                            <el-form-item label="状态:" prop="status" label-width="80px">
                                <el-select v-model="form.status" :disabled="isDetail" placeholder="请选择状态" size="small" class="row_width">
                                    <el-option v-for="item in statusOptions" :key="item.value" :label="item.label" :value="item.value"></el-option>
                                </el-select>
                            </el-form-item>
                        </div>
                        <div class="displayStyle">
                            <el-form-item label="版本:" prop="version">
                                <el-input v-model="form.version" :disabled="isDetail" :maxlength="15" placeholder="请输入版本" size="small" class="row_width"></el-input>
                            </el-form-item>
                        </div>
                    </div>
                    <div>
                        <div class="displayStyle">
                            <el-form-item label="接口名称:" prop="apiName">
                                <el-input v-model="form.apiName" :disabled="isDetail" :maxlength="50" placeholder="请输入接口名称" size="small" class="row_width"></el-input>
                            </el-form-item>
                        </div>
                        <div class="displayStyle">
                            <el-form-item label="接口地址:" prop="apiUrl">
                                <el-input v-model="form.apiUrl" :disabled="isDetail" :maxlength="80" placeholder="请输入接口地址" size="small" class="row_width"></el-input>
                            </el-form-item>
                        </div>
                        <div class="displayStyle">
                            <el-form-item label="模块:" prop="moduleId" label-width="80px">
                                <el-cascader ref="moduleId" :options="moduleIdOptions" :props="{ children: 'childModule', label: 'moduleName', value: 'moduleId' }" v-model="form.moduleId" :disabled="isDetail" :show-all-levels="false" placeholder="请选择模块" size="small" class="row_width"></el-cascader>
                            </el-form-item>
                        </div>
                        <div class="displayStyle">
                            <el-form-item label="调用模块:">
                                <el-select v-model="form.invokeModule" :disabled="isDetail" multiple collapse-tags placeholder="请选择调用模块" size="small" class="row_width">
                                    <el-option v-for="item in invokeModuleOptions" :key="item.moduleId" :label="item.moduleName" :value="item.moduleId"></el-option>
                                </el-select>
                            </el-form-item>
                        </div>
                    </div>
                    <div>
                        <el-form-item label="接口描述:">
                            <el-input v-model="form.apiDesc" :disabled="isDetail" placeholder="请输入接口描述" size="small"></el-input>
                        </el-form-item>
                    </div>
                </el-form>
                <section>
                    <h3 class="el-form-item__label">
                        请求参数:
                        <el-button v-if="!isDetail" plain size="small" class="primaryBtn" @click="editTsCodeTextarea('requestParam')">修改</el-button>
                    </h3>
                    <zk-table ref="table" :data="requestParam" :columns="columns" :selection-type="false" :stripe="false" :border="false" :show-header="true" :show-summary="false" :show-row-hover="true" :show-index="false" :tree-type="true" :is-fold="true" :expand-type="false" children-prop="children" sum-text="sum" index-text="#">
                        <template slot="type" slot-scope="scope">
                            {{ scope.row.$thisNodeType === 'array' ? 'array' : scope.row.$thisNodeType === 'object' ? 'object' : scope.row.value.split('||')[1] }}
                        </template>
                        <template slot="must" slot-scope="scope">
                            {{ scope.row.value ? scope.row.value.split('||')[0] : '' }}
                        </template>
                        <template slot="defaultVal" slot-scope="scope">
                            {{ scope.row.value ? scope.row.value.split('||')[2] : '' }}
                        </template>
                        <template slot="desc" slot-scope="scope">
                            {{ scope.row.value ? scope.row.value.split('||')[3] : '' }}
                        </template>
                        <template slot="exp" slot-scope="scope">
                            {{ scope.row.value ? scope.row.value.split('||')[4] : '' }}
                        </template>
                    </zk-table>
                </section>
                <section>
                    <h3 class="el-form-item__label">
                        响应参数:
                        <el-button v-if="!isDetail" plain size="small" class="primaryBtn" @click="editTsCodeTextarea('responseParam')">修改</el-button>
                    </h3>
                    <zk-table ref="table" :data="responseParam" :columns="columns" :selection-type="false" :stripe="false" :border="false" :show-header="true" :show-summary="false" :show-row-hover="true" :show-index="false" :tree-type="true" :is-fold="true" :expand-type="false" children-prop="children" sum-text="sum" index-text="#">
                        <template slot="type" slot-scope="scope">
                            {{ scope.row.$thisNodeType === 'array' ? 'array' : scope.row.$thisNodeType === 'object' ? 'object' : scope.row.value.split('||')[1] }}
                        </template>
                        <template slot="must" slot-scope="scope">
                            {{ scope.row.value ? scope.row.value.split('||')[0] : '' }}
                        </template>
                        <template slot="defaultVal" slot-scope="scope">
                            {{ scope.row.value ? scope.row.value.split('||')[2] : '' }}
                        </template>
                        <template slot="desc" slot-scope="scope">
                            {{ scope.row.value ? scope.row.value.split('||')[3] : '' }}
                        </template>
                        <template slot="exp" slot-scope="scope">
                            {{ scope.row.value ? scope.row.value.split('||')[4] : '' }}
                        </template>
                    </zk-table>
                </section>
            </div>
            <div v-if="!isDetail" style="margin: 10px 18px;">
                <el-button plain size="small" class="primaryBtn" @click="submitFrom">保存</el-button>
                <el-button plain size="small" class="primaryBtn" @click="$emit('closeNewTab')">返回</el-button>
            </div>
            <div v-else style="margin: 10px 0;display: flex;">
                <section class="instance">
                    <h3 class="el-form-item__label"> 请求实例: </h3>
                    <el-input :rows="8" v-model="requestExample" type="textarea" resize="none" disabled></el-input>
                </section>
                <section class="instance">
                    <h3 class="el-form-item__label"> 响应实例: </h3>
                    <el-input :rows="8" v-model="responseExample" type="textarea" resize="none" disabled></el-input>
                </section>
            </div>
        </div>
        <div v-if="isDetail" class="detailRight">
            <el-tabs>
                <el-tab-pane label="操作记录">
                    <el-table :data="OperationDetail" header-cell-class-name="headerClassName" style="width: 100%;">
                        <el-table-column label="操作人" prop="userName"></el-table-column>
                        <el-table-column label="版本" prop="version"></el-table-column>
                        <el-table-column label="操作时间" width="100">
                            <template slot-scope="scope">
                                <div>
                                    {{ scope.row.createtime | dateFormat('yyyy-MM-dd') }}
                                </div>
                            </template>
                        </el-table-column>
                    </el-table>
                </el-tab-pane>
                <el-tab-pane label="调用模块">
                    {{ invokeModuleStr }}
                </el-tab-pane>
            </el-tabs>
        </div>
        <el-dialog :visible.sync="dialogVisible" :show-close="false" title="参数列表" width="36%" class="primaryDialog">
            <el-input :rows="15" v-model="tsCodeTextarea" type="textarea" placeholder="请输入内容" resize="none"></el-input>
            <div class="dialog-footer">
                <el-button plain size="small" class="primaryBtn" @click="tsCodeTextareaSave">保存</el-button>
                <el-button plain size="small" class="primaryBtn" @click="dialogVisible = false">返回</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
import PlatformMixins from '~/mixins/platform'
import messages from '~/mixins/messages'
import APi from '~/utils/api'
import filters from '~/mixins/filters'
export default {
    name: 'CreateOrEditOrDetail',
    mixins: [PlatformMixins, messages, filters],
    props: {
        isCreate: Boolean,
        isEdit: Boolean,
        isDetail: Boolean
    },
    data () {
        return {
            columns: [
                {
                    label: '参数名称',
                    prop: 'key',
                    minWidth: '300px'
                },
                {
                    label: '类型',
                    type: 'template',
                    minWidth: '80px',
                    template: 'type'
                },
                {
                    label: '是否必须',
                    type: 'template',
                    minWidth: '80px',
                    template: 'must'
                },
                {
                    label: '默认值',
                    type: 'template',
                    minWidth: '150px',
                    template: 'defaultVal'
                },
                {
                    label: '描述',
                    type: 'template',
                    minWidth: '180px',
                    template: 'desc'
                },
                {
                    label: '例子',
                    type: 'template',
                    minWidth: '180px',
                    template: 'exp'
                }
            ],
            dialogVisible: false,
            form: {},
            requestMethodOptions: [
                { label: 'get', value: 'get' },
                { label: 'post', value: 'post' }
            ],
            dataTypeOptions: [
                { label: 'JSON', value: 'JSON' },
                { label: 'XML', value: 'XML' }
            ],
            statusOptions: [
                { label: '有效', value: 0 },
                { label: '废弃', value: 1 }
            ],
            requestParam: [],
            responseParam: [],
            tsCodeTextarea: '',
            editModule: '',
            requestExample: '',
            responseExample: '',
            moduleIdOptions: [],
            rules: {
                requestMethod: { required: true, message: '请选择请求方法', trigger: 'change' },
                dataType: { required: true, message: '请选择数据类型', trigger: 'change' },
                status: { required: true, message: '请选择状态', trigger: 'change' },
                apiName: { required: true, message: '请输入接口名称', trigger: 'blur' },
                apiUrl: { required: true, message: '请输入接口地址', trigger: 'blur' },
                version: { required: true, message: '请输入版本', trigger: 'blur' },
                moduleId: { required: true, message: '请选择模块', trigger: 'change' }
            },
            invokeModuleOptions: [],
            OperationDetail: [],
            invokeModuleStr: ''
        }
    },
    watch: {
        $route (to, from) {
            if (to.name === 'InternalDetail' || to.name === 'InternalEdit') {
                this.queryDetail()
            }
        }
    },
    mounted () {
        APi.queryModuleTree().then(res => {
            this.moduleIdOptions = this.loopTree(res)
            this.invokeModuleOptions = this.recursiveTurnObj(res)
            if (this.isEdit || this.isDetail) {
                this.queryDetail()
            }
        })
    },
    methods: {
        queryDetail () {
            APi.queryInternalDetail({ apiId: this.$route.params.id }).then(res => {
                if (this.isDetail) {
                    APi.queryOperationDetail({ apiId: this.$route.params.id }).then(res => {
                        this.OperationDetail = res
                    })
                }
                const form = Object.assign({}, res)
                this.requestParam = this.jsonTurnTable(JSON.parse(res.requestParam))
                this.requestExample = this.formattingJSON(JSON.stringify(this.jsonTurnExample(JSON.parse(res.requestParam))))
                this.responseParam = this.jsonTurnTable(JSON.parse(res.responseParam))
                this.responseExample = this.formattingJSON(JSON.stringify(this.jsonTurnExample(JSON.parse(res.responseParam))))
                delete form.requestParam
                delete form.responseParam
                form.invokeModule = Object.keys(JSON.parse(form.invokeModule)).map(i => Number(i))
                this.invokeModuleStr = Object.values(JSON.parse(res.invokeModule)).join(',')
                this.$refs.moduleId.$el.childNodes[0].childNodes[1].removeAttribute('placeholder')
                this.$refs.moduleId.$el.childNodes[1].innerText = this.invokeModuleOptions.find(i => i.moduleId === form.moduleId).moduleName
                form.moduleId = [form.moduleId]
                this.form = form
            })
        },
        jsonTurnExample (data) {
            const result = {}
            function recursive (data, obj) {
                for (let i in data) {
                    if (typeof data[i] === 'object') {
                        if (Object.prototype.toString.call(data[i]) === '[object Array]') {
                            obj[i] = [{}]
                            recursive(data[i][0], obj[i][0])
                        } else {
                            obj[i] = {}
                            recursive(data[i], obj[i])
                        }
                    } else {
                        obj[i] = data[i].split('||')[2]
                    }
                }
            }
            recursive(data, result)
            return result
        },
        jsonTurnTable (data) {
            const result = []
            function recursive (data, arr) {
                for (let i in data) {
                    if (typeof data[i] === 'object') {
                        if (Object.prototype.toString.call(data[i]) === '[object Array]') {
                            arr.push({ key: i, value: '', children: data[i], $thisNodeType: 'array' })
                            data[i].forEach(j => {
                                const obj = Object.assign({}, j)
                                arr[arr.length - 1].children = []
                                recursive(obj, arr[arr.length - 1].children)
                            })
                        } else {
                            arr.push({ key: i, value: '', children: [data[i]], $thisNodeType: 'object' })
                            const obj = data[i]
                            arr[arr.length - 1].children = []
                            recursive(obj, arr[arr.length - 1].children)
                        }
                    } else {
                        arr.push({ key: i, value: data[i], $thisNodeType: 'string' })
                    }
                }
            }
            recursive(data, result)
            return result
        },
        tableTurnJson (data) {
            const result = {}
            function recursive (data, obj) {
                data.forEach(i => {
                    if (i.$thisNodeType === 'array') {
                        const { key } = i
                        obj[key] = [{}]
                        recursive(i.children, obj[i.key][0])
                    } else if (i.$thisNodeType === 'object') {
                        const { key, children } = i
                        obj[key] = {}
                        recursive(children, obj[key])
                    } else {
                        obj[i.key] = i.value
                    }
                })
            }
            recursive(data, result)
            return result
        },
        editTsCodeTextarea (reqOrRes) {
            this.dialogVisible = true
            this.editModule = reqOrRes
            const str = JSON.stringify(this.tableTurnJson(this.$data[reqOrRes]))
            this.tsCodeTextarea = this.formattingJSON(str)
        },
        recursiveTurnObj (arr) {
            let max = 0
            const invokeModuleArr = []
            function each (data, floor) {
                data.forEach(i => {
                    if (floor > max) {
                        max = floor
                    }
                    if (Object.prototype.toString.call(i.childModule) === '[object Array]' && i.childModule.length > 0) {
                        each(i.childModule, floor + 1)
                    } else {
                        invokeModuleArr.push(i)
                    }
                })
            }
            each(arr, 0)
            return invokeModuleArr
        },
        submitFrom () {
            this.$refs['form'].validate((valid) => {
                if (valid) {
                    const requestParam = JSON.stringify(this.tableTurnJson(this.requestParam))
                    const responseParam = JSON.stringify(this.tableTurnJson(this.responseParam))
                    const moduleId = this.form.moduleId ? this.form.moduleId[this.form.moduleId.length - 1] : null
                    const invokeModule = {}
                    this.form.invokeModule.forEach((i, k) => {
                        const obj = this.invokeModuleOptions.find(j => j.moduleId === i)
                        if (obj) {
                            invokeModule[obj.moduleId] = obj.moduleName
                        }
                    })
                    const form = Object.assign({}, this.form, { moduleId, invokeModule: JSON.stringify(invokeModule), responseParam, requestParam, teamId: localStorage.getItem('teamId'), userId: localStorage.getItem('userId'), userName: localStorage.getItem('userName') })
                    if (this.isCreate) {
                        APi.createInternal(form).then(res => {
                            this.showSuccess(`新增成功`)
                            this.$emit('closeNewTab')
                        }).catch(() => {
                            this.showError(`新增失败`)
                        })
                    } else {
                        delete form.versionNum
                        form.apiId = this.$route.params.id
                        APi.editInternal(form).then(res => {
                            this.showSuccess(`编辑成功`)
                            this.$emit('closeNewTab')
                        }).catch(() => {
                            this.showError(`新增失败`)
                        })
                    }
                } else {
                    return false
                }
            })
        },
        tsCodeTextareaSave () {
            try {
                const obj = JSON.parse(this.tsCodeTextarea)
                if (this.isJson(obj)) {
                    this.$data[this.editModule] = this.jsonTurnTable(obj)
                    this.dialogVisible = false
                } else {
                    this.showError(`请输入正确格式的JSON数据`)
                }
            } catch (err) {
                this.showError(`请输入正确格式的JSON数据`)
            }
        },
        backfillData (reqOrRes, dataStr) {
            const obj = JSON.parse(dataStr)
            const arr = []
            for (let i in obj) {
                arr.push({ key: i, value: obj[i].split('||') })
            }
            this.$data[reqOrRes] = arr
            return arr
        },
        loopTree (arr, level) {
            const handledTree = arr && arr.map(i => {
                if (Object.prototype.toString.call(i.childModule) === '[object Array]' && i.childModule.length > 0) {
                    this.loopTree(i.childModule)
                } else {
                    delete i.childModule
                }
                return i
            })
            return handledTree
        },
        formattingJSON (str) {
            if (str === '') {
                return false
            } else {
                let res = ''
                for (var i = 0, j = 0, k = 0, ii, ele; i < str.length; i++) { // k:缩进，j:""个数
                    ele = str.charAt(i)
                    if (j % 2 === 0 && ele === '}') {
                        k--
                        for (ii = 0; ii < k; ii++) ele = '    ' + ele
                        ele = '\n' + ele
                    } else if (j % 2 === 0 && ele === '{') {
                        ele += '\n'
                        k++
                        for (ii = 0; ii < k; ii++) ele += '    '
                    } else if (j % 2 === 0 && ele === ',') {
                        ele += '\n'
                        for (ii = 0; ii < k; ii++) ele += '    '
                    } else if (ele === '"') j++
                    res += ele
                }
                return res
            }
        },
        isJson (obj) {
            var isjson =
                typeof obj === 'object' &&
                Object.prototype.toString.call(obj).toLowerCase() ===
                '[object object]' &&
                !obj.length
            return isjson
        }
    }
}
</script>

<style lang="scss" scpoed>
.container-warp {
    position: relative;
    margin: 20px 160px;
    padding: 15px;
    .primaryFrom {
        .el-form-item__label {
            color: #65838e;
            font-size: 16px;
            font-weight: 400;
        }
    }
    .displayStyle {
        display: inline-block;
        .row_width {
            width: 154px;
            height: 36px;
        }
    }
    .primaryBtn {
        padding: 8px 16px;
        background-color: #65838e;
        color: #fff;
        border: none;
        &:hover,
        &:focus {
            background-color: #65838e;
            color: #fff;
        }
    }
    .resetPadding {
        padding: 12px 20px;
    }
    .instance {
        margin: 0 10px;
        flex: 1;
    }
}
.isDetail {
    margin-right: 15px;
    margin-left: 15px;
}
.detailLeft {
    margin-right: 15px;
    margin-left: 15px;
    display: inline-block;
    width: 75%;
}
.detailRight {
    position: absolute;
    top: 0;
    bottom: 0;
    display: inline-block;
    background-color: #fff;
    width: 20%;
    height: 100%;
    margin-top: 15px;
    padding: 10px;
}
.el-table__header-wrapper {
    background-color: #fff;
    .has-gutter {
        th {
            background-color: #fff;
            border: none;
        }
    }
}
.el-table td,
.el-table th.is-leaf {
    border: none;
}
.el-table::before {
    background-color: #fff;
}
</style>

<style lang="scss">
.primaryDialog {
    .el-dialog {
        .el-dialog__header {
            text-align: center;
            padding: 20px 0;
            border-bottom: 1px solid #65838e;
            margin: 0 25px;
            .el-dialog__title {
                font-size: 18px;
                color: #65838e;
            }
        }
        .el-dialog__body {
            padding: 30px;
        }
        .dialog-footer {
            margin-top: 15px;
            text-align: justify;
            height: 28px;
            &:after {
                display: inline-block;
                width: 100%;
                content: "";
            }
        }
    }
}
.el-table--scrollable-x .el-table__body-wrapper {
    overflow-x: hidden;
}
.el-tabs__item.is-active {
    color: #65838e;
}
.el-tabs__item:hover {
    color: #e0eaec;
}
.el-tabs__active-bar {
    background-color: #65838e;
}
</style>
