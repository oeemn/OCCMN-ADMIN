<template>
    <!-- 搜索 -->
    <div class="search">
        <div class="search__left">
            <el-input v-model="keyword" @change="search" clearable placeholder="输入姓名搜索">
                <template #append>
                    <el-button :icon="Search" />
                </template>
            </el-input>
        </div>
        <div class="search__right">
            <el-button type="primary" :icon="CirclePlusFilled" @click="addAccount">新增</el-button>
        </div>
    </div>
    
    <!-- 列表 -->
    <el-table :data="list" :row-class-name="tableRowClassName">
        <el-table-column type="index" label="#" />
        <el-table-column prop="id" label="编号" />
        <el-table-column prop="name" label="姓名" />
        <el-table-column prop="password" label="密码" />
        <el-table-column prop="createTime" label="创建时间" />
        <el-table-column prop="status" label="状态">
            <template #default="scope">
                <el-switch
                    v-model="scope.row.status"
                    inline-prompt
                    style="--el-switch-on-color: #13ce66; --el-switch-off-color: #ff4949"
                    active-text="启用"
                    inactive-text="禁用"
                />
            </template>
        </el-table-column>
        <el-table-column label="操作" align="center" width="230">
            <template #default="scope">
                <el-button type="danger" @click="delAccount(scope.row)">删除</el-button>
                <el-button type="primary" @click="editAccount(scope.row)">编辑</el-button>
            </template>
        </el-table-column>
    </el-table>

    <!-- 分页 -->
    <el-pagination background
        hide-on-single-page
        layout="sizes, prev, pager, next, total, jumper"
        :total="1000"
        :page-sizes="[10, 15, 20, 25, 30]"
        style="margin-top: 20px;"
    />

    <!-- 新增/编辑 -->
    <el-dialog v-model="dialogVisible" :title="dialogTitle" width="500px">
        <el-form :model="account">
            <el-form-item label="姓名">
                <el-input v-model="account.name" />
            </el-form-item>
            <el-form-item label="密码">
                <el-input v-model="account.password" type="password" :disabled="!!account.id" />
            </el-form-item>
        </el-form>
        <template #footer>
            <span class="dialog-footer">
                <el-button @click="dialogVisible = false">取消</el-button>
                <el-button type="primary" @click="dialogVisible = false">确定</el-button>
            </span>
        </template>
    </el-dialog>
</template>

<script lang="ts" setup>
    import { ref } from 'vue'
    import { IAccount, initAccount } from '../../models/IAccount'
    import { Search, CirclePlusFilled } from '@element-plus/icons-vue'
    import useConfirm from '../../hooks/useConfirm'

    // 列表
    const list = ref<IAccount[]>([
        { id: '001', name: '章三', password: '001233', createTime: '2023-01-31', status: true },
        { id: '001', name: '章三', password: '001233', createTime: '2023-01-31', status: true },
        { id: '001', name: '章三', password: '001233', createTime: '2023-01-31', status: true },
        { id: '001', name: '章三', password: '001233', createTime: '2023-01-31', status: false },
        { id: '001', name: '章三', password: '001233', createTime: '2023-01-31', status: true },
        { id: '001', name: '章三', password: '001233', createTime: '2023-01-31', status: true },
        { id: '001', name: '章三', password: '001233', createTime: '2023-01-31', status: true },
        { id: '001', name: '章三', password: '001233', createTime: '2023-01-31', status: true },
        { id: '001', name: '章三', password: '001233', createTime: '2023-01-31', status: false },
        { id: '001', name: '章三', password: '001233', createTime: '2023-01-31', status: true }
    ])
    const tableRowClassName = ({ row } : { row: IAccount }) => {
        if (!row.status) {
            return 'danger-row'
        }
        return ''
    }

    // 搜索
    const keyword = ref('')
    const search = () => {

    }

    // 删除
    const { confirm } = useConfirm()
    const delAccount = (row: IAccount) => {
        confirm(`确定删除用户 [${row.name}] 吗？`, () => {})
    }

    // 编辑新增
    const dialogVisible = ref(false)
    const dialogTitle = ref('')
    const account = ref<IAccount>(initAccount())
    const addAccount = () => {
        account.value = initAccount()
        dialogTitle.value = '新增账户'
        dialogVisible.value = true
    }
    const editAccount = (row: IAccount) => {
        account.value = row
        dialogTitle.value = '编辑账户'
        dialogVisible.value = true
    }
</script>

<style scoped>
    
</style>