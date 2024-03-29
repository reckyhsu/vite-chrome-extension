<template>
	<div class="space-gugu">
		<!-- 是否开启功能的开关 -->
		<div class="space-gugu__left">
			<div class="space-gugu__switch">
				<div class="space-gugu__switch__label">是否展示咕咕数据</div>
				<el-switch v-model="isOpen" @change="changIsOpen"></el-switch>
			</div>
			<div v-if="upGugu.mtime">
				<div v-if="upGugu.mtime === -1">没有关注这个 up 主</div>
				<div v-if="upGugu.mtime > 0">关注时间: {{ formatDateToChinese(upGugu.mtime) }}</div>
			</div>
		</div>
		<!-- 展示数据区域 -->
		<div v-if="isOpen" class="space-gugu__body">
			<div v-if="upGugu.videosNum === -1">
				点击 那里的 👉 <el-icon><Download /></el-icon> 即可获取 up 主咕咕数据
			</div>
			<div v-else>
				<div v-if="upGugu.videosNum === 0">这个 up 主还没有视频哦</div>
				<div v-else>
					<div v-if="upGugu.currentGuguLength">
						<div>
							当前咕咕时长
							<span class="space-gugu__gugu-length space-gugu__gugu-length--current">{{
								getTimeDiff(upGugu.currentGuguLength)
							}}</span>
						</div>
						<div>
							平均更新频率
							<span class="space-gugu__gugu-length space-gugu__gugu-length--average">{{
								getTimeDiff(upGugu.averageGuguLength)
							}}</span>
						</div>
						<div>
							最多咕咕时长
							<span class="space-gugu__gugu-length space-gugu__gugu-length--max">{{
								getTimeDiff(upGugu.maxGuguLength)
							}}</span>
						</div>
					</div>
					<div v-else>
						<!-- 显示进度条 -->
						<div style="width: 100%">
							<div>
								当前获取到的视频数量：{{ upGugu.currentHaveVideosNum }}，共需要获取{{
									upGugu.videosNum
								}}
							</div>
							<el-progress
								:text-inside="true"
								:stroke-width="26"
								:percentage="getProgress(upGugu.currentHaveVideosNum, upGugu.videosNum)"
								stroke-linecap="square"
							/>
						</div>
					</div>
				</div>
			</div>
		</div>
		<!-- 操作区域 -->
		<div v-if="isOpen" class="space-gugu__operate">
			<!-- 加载按钮 -->
			<el-button
				v-if="handlingMid !== upGugu.mid"
				:icon="upGugu.videosNum === -1 ? Download : Refresh"
				size="small"
				circle
				@click.stop="refresh"
			/>
			<!-- 取消按钮 -->
			<el-button
				v-else
				:icon="CircleClose"
				size="small"
				circle
				type="warning"
				@click.stop="cancelRefresh"
			></el-button>
			<!-- 删除按钮 -->
			<el-popconfirm title="确认从本地删除该 up 主的信息吗?" @confirm="deleteUp">
				<template #reference>
					<el-button type="danger" :icon="Delete" circle size="small" />
				</template>
			</el-popconfirm>
		</div>
		<!-- 展开所有 up 主信息的面板 -->
		<div class="space-gugu__all-btn">
			<el-button size="small" @click="open">打开关注的 up 主列表</el-button>
		</div>
	</div>
</template>

<script setup lang="ts">
import { isOpen, changIsOpen, upGugu, onLoad, removeGuguTag, getIsLogin } from '../../../utils/up-space-gugu/index';
import { Download, Refresh, CircleClose, Delete } from '@element-plus/icons-vue';
import { getTimeDiff, formatDateToChinese } from '../../../utils/common';
import { useGugu } from '../../../utils/useGugu';
import { ElMessage } from 'element-plus';

const { handlingMid, refreshOneUpGugu, cancelRefresh, drawerVisible, init, deleteUpGugu } = useGugu();

// 给当前插件一个 id
const guguExtensionId = String(Date.now());

const refresh = async () => {
	// 更新数据
	const isContinue = await refreshOneUpGugu(upGugu.value);
	// 插入视频 dom
	if (isContinue) {
		onLoad();
	}
};

const deleteUp = () => {
	deleteUpGugu(upGugu.value);
	removeGuguTag();
};

// 计算视频列表获取进度
const getProgress = (currentNum: number, videosNum: number) => {
	if (videosNum === -1) {
		return 0;
	}

	if (videosNum === 0) {
		return 0;
	}

	return Number(((currentNum / videosNum) * 100).toFixed(2));
};

// 打开遮罩层
const open = async () => {
	if (!getIsLogin()) {
		ElMessage.error('还未登录哦, 无法获取关注列表, 先登录吧');
		return;
	}
	const activeGuguExtensionId = localStorage.getItem('active_gugu_extension_id');
	if (!activeGuguExtensionId) {
		localStorage.setItem('active_gugu_extension_id', guguExtensionId);
	} else if (activeGuguExtensionId !== guguExtensionId) {
		ElMessage.error('无法打开, 本功能仅支持在一个窗口使用');
		return;
	}
	document.getElementsByTagName('body')[0].style.overflow = 'hidden';
	// 继续请求观众数据
	drawerVisible.value = true;
	init();
};
</script>

<style lang="less">
.space-gugu {
	height: 86px;
	display: flex;
	align-items: center;
	position: relative;
	top: -85px;
	padding: 10px;
	box-sizing: border-box;
	color: white;
	transition: width 2s;
	background-color: #00000080;

	&__left {
		display: flex;
		flex-direction: column;
		justify-content: space-around;
		height: 100%;
	}

	&__body {
		margin-left: 33px;
		width: 300px;
	}

	&__operate {
		margin-left: 10px;
	}

	&__switch {
		display: flex;
		align-items: center;
		user-select: none;
		&__label {
			color: white;
			margin-right: 10px;
		}
	}
	&__all-btn {
		height: 100%;
		display: flex;
		align-items: center;
		position: absolute;
		right: 10px;
	}
	&__gugu-length {
		margin: 0 10px;
		font-weight: 600;
		&--current {
			color: #0085ff;
		}
		&--average {
			color: #10c80a;
		}
		&--max {
			color: #ea0000;
		}
	}
}
</style>
