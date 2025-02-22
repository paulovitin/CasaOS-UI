<!--
  * @LastEditors: zhanghengxin ezreal.zhang@icewhale.org
  * @LastEditTime: 2023/4/24 上午11:20
  * @FilePath: /CasaOS-UI/src/components/filebrowser/viewers/VideoPlayer.vue
  * @Description:
  *
  * Copyright (c) 2023 by IceWhale, All Rights Reserved.

  -->

<!--
 * @Author: JerryK
 * @Date: 2022-03-04 18:55:13
 * @LastEditors: Jerryk jerry@icewhale.org
 * @LastEditTime: 2023-03-10 17:21:48
 * @Description: 
 * @FilePath: /CasaOS-UI/src/components/filebrowser/viewers/VideoPlayer.vue
-->
<template>
	<div class="overlay">
		<header class="modal-card-head">
			<div class="is-flex-grow-1 is-flex">
				<!-- Title Start -->
				<h3 class="title is-5 one-line">{{ item.name }}</h3>
				<!-- Title End -->
			</div>
			<div class="is-flex is-align-items-center">
				<!-- Download File Button Start -->
				<b-button
					:label="$t('Download')"
					class="mr-2"
					icon-left="download"
					rounded
					size="is-small"
					type="is-primary"
					@click="download"
				/>
				<!-- Download File Button End -->

				<!-- Close Button Start -->
				<div class="close-button" @click="close">
					<b-icon icon="close" pack="casa"></b-icon>
				</div>
				<!-- Close File Button End -->
			</div>
		</header>

		<!-- Player Start -->
		<div
			class="is-flex is-justify-content-center is-align-items-center is-flex-grow-1 v-container video"
		>
			<div
				:class="{ 'is-align-items-center': isAudio }"
				class="video-container"
			>
				<vue-plyr
					v-if="isVideo"
					key="video-player"
					ref="plyr"
					:options="options"
				>
					<video controls crossorigin playsinline>
						<source :src="getFileUrl(item)" type="video/mp4"/>
					</video>
				</vue-plyr>
				<vue-plyr
					v-if="isAudio"
					key="audio-player"
					ref="plyr"
					class="plyr-audio"
				>
					<audio controls crossorigin playsinline>
						<source :src="getFileUrl(item)" :type="'audio/' + ext"/>
					</audio>
				</vue-plyr>
			</div>
		</div>
		<!-- Player Start -->

		<!-- Player Footer Start -->
		<div class="v-footer is-flex is-justify-content-center">
			<!-- <div class="buttons video-footer" v-if="isVideo">
			  <b-tooltip :label="$t('Play in IINA')" type="is-dark">
				<b-button type="is-iina" class="mr-1" rounded @click="playVideo(item,'iina://weblink?url=')">IINA</b-button>
			  </b-tooltip>
					  <b-tooltip :label="$t('Play in PotPlayer')" type="is-dark">
						<b-button type="is-potplayer" class="mr-1 ml-1" rounded @click="playVideo(item,'potplayer://')">PotPlayer
						</b-button>
					  </b-tooltip>
					  <b-tooltip :label="$t('Play in VLC')" type="is-dark">
						<b-button type="is-vlc" class=" ml-1" rounded @click="playVideo(item,'vlc://')">VLC</b-button>
					  </b-tooltip>
					</div> -->
		</div>
		<!-- Player Footer End -->
	</div>
</template>

<script>
import {mixin} from "@/mixins/mixin";
// import VuePlyr from 'vue-plyr'
import "vue-plyr/dist/vue-plyr.css";

export default {
	mixins: [mixin],
	props: {
		item: {
			type: Object,
			default: () => {
				return {
					path: "/DATA/5 Seconds Of Summer - Amnesia.mp4",
					name: "5 Seconds Of Summer - Amnesia.mp4",
				};
			},
		},
		list: {
			type: Array,
			default: () => {
				return [];
			},
		},
	},
	components: {
		VuePlyr: () => import("vue-plyr"),
	},
	data() {
		return {
			type: "",
			ext: "",
			player: null,
			options: {
				controls: [
					"play-large", // The large play button in the center
					"restart", // Restart playback
					"rewind", // Rewind by the seek time (default 10 seconds)
					"play", // Play/pause playback
					"fast-forward", // Fast forward by the seek time (default 10 seconds)
					"progress", // The progress bar and scrubber for playback and buffering
					"current-time", // The current time of playback
					"duration", // The full duration of the media
					"mute", // Toggle mute
					"volume", // Volume control
					"captions", // Toggle captions
					"settings", // Settings menu
					"pip", // Picture-in-picture (currently Safari only)
					"airplay", // Airplay (currently Safari only)
					"download", // Show a download button with a link to either the current source or a custom URL you specify in your options
					"fullscreen", // Toggle fullscreen
				],
			},
		};
	},
	computed: {
		isVideo() {
			return this.type == "video-x-generic";
		},
		isAudio() {
			return this.type == "audio-x-generic";
		},
	},
	mounted() {
		this.ext = this.getFileExt(this.item);
		Object.keys(this.typeMap).forEach((_type) => {
			const extensions = this.typeMap[_type];
			if (extensions.indexOf(this.ext.toLowerCase()) > -1) {
				this.type = _type;
			}
		});
	},
	methods: {
		download() {
			this.downloadFile(this.item);
		},
		close() {
			this.$emit("close");
		},
	},
	beforeDestroy() {
		this.$refs.plyr.player.destroy();
	},
};
</script>

<style>
.plyr {
	height: 100%;
	width: 100%;
}

.plyr--audio {
	height: auto !important;
}

.plyr__video-wrapper {
	height: 100%;
}

.plyr__video-wrapper iframe {
	width: 100%;
	height: 100%;
}
</style>
