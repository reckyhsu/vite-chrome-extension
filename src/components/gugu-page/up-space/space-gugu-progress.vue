<template>
	<el-progress style="width: 100%" :text-inside="true" :stroke-width="26" :percentage="progress" />
</template>

<script setup lang="ts">
import { upGugu } from '../../../utils/up-space-gugu';
import { computed } from 'vue';
// 计算视频列表获取进度
const progress = computed(() => {
	if (upGugu.value.videosNum === -1) {
		return 0;
	}

	if (upGugu.value.videosNum === 0) {
		return 0;
	}

	const progress = Number(((upGugu.value.currentHaveVideosNum / upGugu.value.videosNum) * 100).toFixed(2));
	return progress;
});
</script>

<style></style>
