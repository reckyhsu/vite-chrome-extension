<template>
	<!-- 控制区域 -->
	<div class="gugu-table__drawer" :class="{ 'gugu-table__drawer--open__control': isShowControlDrawer }">
		<div class="gugu-table__drawer__switch" @click="isShowControlDrawer = !isShowControlDrawer">
			{{ isShowControlDrawer ? '关闭面板' : '打开面板' }}
		</div>
		<div class="control">
			<el-divider content-position="center">排序</el-divider>
			<el-tooltip
				class="box-item"
				effect="dark"
				:disabled="isBatchRequesting === ''"
				content="需要先取消批量操作"
				placement="top"
			>
				<el-select
					v-model="sortType"
					class="m-2"
					placeholder="选择排序方式"
					:disabled="isBatchRequesting !== ''"
					size="small"
				>
					<el-option
						v-for="item in sortTypeOptions"
						:key="item.value"
						:label="item.label"
						:value="item.value"
					/>
				</el-select>
			</el-tooltip>

			<el-divider content-position="center">是否降序排序</el-divider>
			<el-radio v-model="sortOrder" :label="false" size="large">降序</el-radio>
			<el-radio v-model="sortOrder" :label="true" size="large">升序</el-radio>

			<el-divider content-position="center">隐藏</el-divider>
			<div>
				<el-checkbox v-model="isHideUnFetchUp" label="还没获取的 up 主" />
				<el-checkbox v-model="isHideNoVideosUp" label="没有视频的 up 主" />
			</div>

			<el-divider content-position="center">是否加入自己</el-divider>
			<el-switch v-model="isAddSelf" />

			<el-divider content-position="center">搜索 up 主</el-divider>
			<el-input v-model="userNameFilter"></el-input>

			<el-divider content-position="center">批量操作</el-divider>
			<GuguTableControlBatch />
		</div>
	</div>
</template>

<script setup>
import { useGugu } from '../../utils/useGugu';
import GuguTableControlBatch from './gugu-table-control-batch.vue';
const {
	sortType,
	isAddSelf,
	sortOrder,
	userNameFilter,
	isShowControlDrawer,
	isHideUnFetchUp,
	isHideNoVideosUp,
	isBatchRequesting,
} = useGugu();
const sortTypeOptions = [
	{
		label: '根据关注时间',
		value: 'mtime',
	},
	{
		label: '根据视频数量',
		value: 'videosNum',
	},
	{
		label: '根据当前咕咕时长',
		value: 'currentGuguLength',
	},
	{
		label: '根据平均更新频率',
		value: 'averageGuguLength',
	},
	{
		label: '根据最大咕咕时长',
		value: 'maxGuguLength',
	},
];
</script>

<style lang="less" scoped>
.gugu-table__drawer {
	width: 21%;
	height: 100vh;
	background-color: white;
	box-shadow: 0px 0px 26px 3px #7c7c7c;
	position: absolute;
	right: -21%;
	transition: right 0.3s;
	padding: 30px;
	box-sizing: border-box;
	&--open__control {
		right: 0;
	}

	&__switch {
		width: 44px;
		height: 120px;
		background-color: #ffffff;
		position: absolute;
		left: -44px;
		padding: 13px;
		box-sizing: border-box;
		display: flex;
		align-items: center;
		justify-content: center;
		top: calc(44% - 60px);
		border: 1px solid #f0f0f0;
		box-shadow: -10px 9px 9px -9px #edebeb;
		border-right: 0;
		border-radius: 7px 0 0 7px;
		user-select: none;
		cursor: pointer;
		color: #bebebf;
		&:hover {
			background-color: rgb(245, 245, 245);
		}
	}
}
</style>
