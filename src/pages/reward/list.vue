<template>
    <!-- 搜索 -->
    <!-- <div class="search">
        <div class="search__left">
            <div class="search__left-item">
                <el-input v-model="params.keyWord" @change="search" clearable placeholder="输入名称搜索">
                    <template #append>
                        <el-button :icon="Search" />
                    </template>
                </el-input>
            </div>
            <div class="search__left-item">
                <el-select v-model="params.status" @change="search" placeholder="分类状态" clearable>
                    <el-option label="启用" :value="true"></el-option>
                    <el-option label="禁用" :value="false"></el-option>
                </el-select>
            </div>
        </div>
        <div class="search__right">
            <el-button type="primary" :icon="CirclePlusFilled" @click="add('分类')">新增</el-button>
        </div>
    </div> -->
    
    <!-- 列表 -->
    <el-table :data="list" stripe>
        <el-table-column type="index" label="#" width="200" />
		<el-table-column prop="createTime" label="打赏时间">
            <template #default="scope">
                {{ dateFormat(scope.row.createTime) }}
            </template>
        </el-table-column>
        <el-table-column prop="amount" label="打赏金额" align="center">
			<template #default="scope">
                <span class="money">￥{{ scope.row.amount.toFixed(2) }}</span>
            </template>
		</el-table-column>
        <el-table-column prop="payMethod" label="支付方式" align="center" />
        <el-table-column label="操作" align="right" width="230">
            <template #default="scope">
                <el-button type="danger" :icon="Delete" @click="remove(scope.row)">删除</el-button>
                <el-button type="primary" :icon="Edit" @click="edit(scope.row, '分类')">编辑</el-button>
            </template>
        </el-table-column>
    </el-table>

    <!-- 分页 -->
    <el-pagination background
        hide-on-single-page
        layout="sizes, prev, pager, next, total, jumper"
        :total="paging.total"
        v-model:page-size="paging.pageSize"
        v-model:current-page="paging.pageIndex"
        :page-sizes="[10, 15, 20, 25, 30]"
        @current-change="getList"
        @size-change="getList"
        style="margin-top: 20px;"
    />

    <!-- 新增/编辑 -->
    <el-dialog v-model="dialogVisible" :title="dialogTitle" width="500px">
        <el-form :model="reward">
            <el-form-item label="打赏金额">
                <el-input v-model.number="reward.amount" type="number" />
            </el-form-item>
            <el-form-item label="支付方式">
                <el-input v-model="reward.payMethod" />
            </el-form-item>
        </el-form>
        <template #footer>
            <span class="dialog-footer">
                <el-button @click="dialogVisible = false">取消</el-button>
                <el-button type="primary" @click="save">确定</el-button>
            </span>
        </template>
    </el-dialog>
</template>

<script lang="ts" setup>
    import { reactive } from 'vue'
	import { IReward, initReward } from '../../models/IReward'
    import { Search, CirclePlusFilled, Delete, Edit } from '@element-plus/icons-vue'
    import { dateFormat } from '@/utils/dateFormat'
	import $apiReward from '@/apis/reward'
    import useConfirm from '../../hooks/useConfirm'
	import useListPage from '../../hooks/useListPage'
    import useModify from '@/hooks/useModify'

    // 列表
    // const params = reactive({ keyWord: '', status: '' })
	const {
        list,
        paging,
        getList,
        search
    } = useListPage<IReward>($apiReward.list, {})
    getList()
	
    // 删除
    const { confirm } = useConfirm()
    const remove = (row: IReward) => {
        confirm(`确定删除打赏 [${row.amount}] 吗？`, () => {
			$apiReward.delete(row.id!).then(() => {
                getList()
            })
		})
    }

    // 编辑新增
    const {
        dialogVisible,
        dialogTitle,
        model: reward,
        add,
        edit
    } = useModify<IReward>(initReward)
	const save = async () => {
        if (reward.value.id) {
            await $apiReward.update(reward.value)
        } else {
            await $apiReward.create(reward.value)
        }
        dialogVisible.value = false
        getList()
    }
</script>

<style>
	.money {
		font-weight: bold;
		color: darkcyan;
	}
</style>