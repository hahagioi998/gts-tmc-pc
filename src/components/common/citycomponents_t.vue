<template>
	<div class="citycom">
		<div class="citcom_left" v-if="cityleft">
			<el-input
				:disabled="citylefts"
				v-model="cityleft.name"
				@input="inpu('left')"
				@focus="foinput('left')"
				@blur="blurinput('left')"
				style="border: 0;"
				class="inputs"
				size="medium"
				placeholder="出发城市"
			></el-input>
		</div>
		<div class="citzt"><i @click="exchange" class="iconfont" style="font-size:22px;color: #828282;cursor: pointer;">&#xe6f7;</i></div>
		<div class="citcom_left" v-if="cityright">
			<el-input
				:disabled="cityrights"
				v-model="cityright.name"
				@input="inpu('right')"
				@focus="foinput('right')"
				@blur="blurinput('right')"
				style="border: 0;"
				class="inputs"
				size="medium"
				placeholder="到达城市"
			></el-input>
		</div>
		<div @mouseover="isctyslt = true" @mouseleave="isctyslt = false" :class="[opencity ? 'openleft' : 'openright', 'openst']" v-if="mostcity">
			<div class="open_tar" v-if="citylist">
				<div class="opne_lis" @mouseover="selectStyle(item)" :class="citcheck == item.id ? 'opne_liskd' : ''" v-for="(item, index) in citylist" :key="index">
					{{ item.name }}
				</div>
			</div>
			<div class="cihotsy" v-if="citcheck == 'rot' && hotCitys">
				<div class="cihots" @click="okcitys(item)" v-for="(item, index) in hotCitys" :key="index">{{ item.name }}</div>
			</div>
			<div class="cist" v-if="citcheck != 'rot'">
				<div class="cihots" v-for="(item, index) in stCitys" :key="index">
					<div class="scttab">{{ item.letter }}</div>
					<div class="monstlist">
						<div class="clstname" @click="okcitys(items)" v-for="(items, indexs) in item.list" :key="indexs" v-if="indexs < 16">{{ items.name }}</div>
					</div>
				</div>
			</div>
		</div>
		<div @mouseover="isctyslts = true" @mouseleave="isctyslts = false" :class="[opencity ? 'searchsleft' : 'searchsright', 'searchs']" v-if="mostcitys && searlist">
			<div class="searlist" v-for="(item, index) in searlist" @click="searck(item)" :key="index">
				<div>{{ item.name }}</div><div>{{ item.id }}</div>
			</div>
		</div>
	</div>
</template>

<script>
import citys from '../../../static/js/train.js';
export default {
	props: {
		ctiys: {
			type: Array
		},
		iskes: {
			type: Number
		},
		issera: {
			type: Boolean
		}
	},
	data() {
		return {
			cityrights: false,
			citylefts: false,
			searlist: [], //搜索机场值
			isctyslt: false,
			isctyslts: false,
			cityleft: { name: '', id: '' }, //左边输入框的值
			cityright: { name: '', id: '' }, //右边输入框的值
			opencity: true, //左边还是右边
			mostcity: false, //是否显示机场列表
			mostcitys: false, //是否显示搜索值
			citcheck: 'rot',
			hotCitys: [], //热门机场
			stCitys: [], //所有机场
			oldcitylist: [],
			citylist: [
				{
					name: '热门',
					id: 'rot',
					su: []
				},
				{
					name: 'ABCD',
					id: 'ABCD',
					su: ['A', 'B', 'C', 'D']
				},
				{
					name: 'EFGH',
					id: 'EFGH',
					su: ['E', 'F', 'G', 'H']
				},
				{
					name: 'JKLM',
					id: 'JKLM',
					su: ['J', 'K', 'L', 'M']
				},
				{
					name: 'NPQR',
					id: 'NPQR',
					su: ['N', 'P', 'Q', 'R']
				},
				{
					name: 'STWX',
					id: 'STWX',
					su: ['S', 'T', 'W', 'X']
				},
				{
					name: 'YZ',
					id: 'YZ',
					su: ['Y', 'Z']
				}
			]
		};
	},
	mounted() {
		if (this.issera) {
			this.citylefts = true;
			this.cityrights = true;
		}
		let ct = this.ctiys;
		if (this.iskes == 1 || this.iskes == 4) {
			this.citylefts = true;
		}
		if (this.iskes == 1) {
			//1为事前 4为改签
			this.cityrights = true;
		}
		if (ct.length > 0) {
			this.cityleft = ct[0];
			this.cityright = ct[1];
		}
	},
	watch: {
		ctiys(item) {
			this.cityleft = item[0];
			this.cityright = item[1];
			this.$emit('changers', [this.cityleft, this.cityright]);
		},
		issera(item) {
			if (item) {
				this.citylefts = true;
				this.cityrights = true;
			} else {
				this.citylefts = false;
				this.cityrights = false;
			}
		}
	},
	methods: {
		exchange() {
			if (this.issera) {
				return;
			}
			let va = this.cityleft;
			this.cityleft = this.cityright;
			this.cityright = va;
			this.$emit('changers', [this.cityleft, this.cityright]);
		},
		foinput(vi) {
			//输入框获取焦点
			if (vi == 'left') {
				this.opencity = true;
			} else {
				this.opencity = false;
			}
			this.isctyslt = false;
			this.isctyslts = false;
			this.selectStyle(this.citylist[0]); //默认显示热点机场
			this.mostcity = true;
		},
		okcitys(tiem) {
			//点击当前机场
			if (this.opencity) {
				this.cityleft = tiem;
			} else {
				this.cityright = tiem;
			}
			this.mostcity = false;
			this.$emit('changers', [this.cityleft, this.cityright]);
		},
		blurinput(it) {
			//输入框失去焦点
			if (!this.isctyslt) {
				this.mostcity = false;
			}
			if (!this.isctyslts) {
				if (this.mostcitys) {
					if (it == 'left') {
						if (this.oldcitylist.length > 0) {
							this.cityleft = {
								name: this.oldcitylist[0].name,
								id: this.oldcitylist[0].id
							};
						} else {
							this.cityleft = {
								name: '',
								id: ''
							};
						}
					} else if (it == 'right') {
						if (this.oldcitylist.length > 0) {
							this.cityright = {
								name: this.oldcitylist[0].name,
								id: this.oldcitylist[0].id
							};
						} else {
							this.cityright = {
								name: '',
								id: ''
							};
						}
					}
					this.$emit('changers', [this.cityleft, this.cityright]);
					this.mostcitys = false;
				}
			}
		},
		inpu(it) {
			//输入框值发生变化
			this.mostcity = false;
			let va = '';
			if (it == 'left') {
				if (this.cityleft.name.length > 0) {
					this.mostcitys = true;
					va = this.cityleft.name;
				} else {
					this.mostcitys = false;
					return;
				}
			} else {
				if (this.cityright.name.length > 0) {
					this.mostcitys = true;
					va = this.cityright.name;
				} else {
					this.mostcitys = false;
					return;
				}
			}
			let address = citys.addressTrainAll; //所有车站
			this.searlist = [];
			for (let i in address) {
				if (address[i].name.indexOf(va) != -1) {
					this.searlist.push({
						id: address[i].code,
						name: address[i].name
					});
				}
			}
			if (this.searlist.length > 0) {
				this.oldcitylist = [];
				this.oldcitylist.push(this.searlist[0]);
			}
			this.$emit('changers', [this.cityleft, this.cityright]);
		},
		searck(itm) {
			//选择当前输入框值
			if (this.opencity) {
				this.cityleft = itm;
			} else {
				this.cityright = itm;
			}
			this.mostcitys = false;
			this.$emit('changers', [this.cityleft, this.cityright]);
		},
		selectStyle(item) {
			this.cityslist(item);
			this.citcheck = item.id;
		},
		cityslist(its) {
			//弹出层选择城市
			if (its.id == 'rot') {
				this.hotCitys = [];
				let hot = citys.addressTrainHot; //热门城市
				for (let i = 0; i < hot.length; i++) {
					this.hotCitys.push({
						name: hot[i].name,
						id: hot[i].code
					});
				}
			} else {
				let address = citys.addressTrainAll; //所有车站
				let ct = [];
				for (let k in its.su) {
					ct.push({
						letter: its.su[k],
						list: []
					});
				}
				for (let i = 0; i < address.length; i++) {
					let pys = address[i].code.substring(0, 1).toUpperCase(); //获取改城市的首字母
					for (var k = 0; k < ct.length; k++) {
						if (pys == ct[k].letter) {
							ct[k].list.push({
								name: address[i].name,
								id: address[i].code
							});
						}
					}
				}
				this.stCitys = ct;
			}
		}
	}
};
</script>

<style scoped lang="less">
.citycom {
	position: relative;
	width: 100%;
	display: flex;
	height: 100%;
	line-height: 40px;
	color: #abb7c2;
	.citcom_left {
		width: calc(44% - 28px);
		
		display: flex;
		border: 1px solid #e4e4e4;
		border-radius: 4px;
		padding: 0 14px;
		.inputs/deep/.el-input__inner {
			border: 0;
			padding: 0;
			font-size: 16px;
		}
	}
	.citzt {
		width: 12%;
		height: 100%;
		text-align: center;
	}
	.openleft {
		left: 15px;
	}
	.openright {
		left: 60%;
	}
	.openst {
		position: absolute;
		padding: 10px 25px;
		background: #ffffff;
		top: 35px;
		box-shadow: 0 4px 4px 0 rgba(0, 0, 0, 0.1);
		z-index: 9999;
		.open_tar {
			width: 100%;
			height: 40px;
			line-height: 40px;
			display: flex;
			color: #000000;
			.opne_lis {
				width: 80px;
				text-align: center;
				border-bottom: 1px solid #abb7c2;
			}
			.opne_liskd {
				color: #409eff;
				border-bottom: 1px solid #409eff;
			}
		}
		.cihotsy {
			display: flex;
			flex-wrap: wrap;
			color: #000000;
			font-size: 14px;
			.cihots {
				height: 50px;
				line-height: 50px;
				padding: 0 15px;
				cursor: pointer;
			}
			.cihots:hover {
				color: red;
			}
		}
		.cist {
			font-size: 14px;
			color: #000000;
			.cihots {
				padding-top: 10px;
				.scttab {
					text-indent: 15px;
					height: 30px;
					line-height: 30px;
				}
				.monstlist {
					display: flex;
					flex-wrap: wrap;
					.clstname {
						height: 30px;
						line-height: 30px;
						padding: 0 15px;
						cursor: pointer;
					}
					.clstname:hover {
						color: red;
					}
				}
			}
		}
	}
	.searchs {
		color: #000000;
		position: absolute;
		background: #ffffff;
		top: 70px;
		width: 350px;
		z-index: 9999;
		box-shadow: 0 4px 10px 0 rgba(0, 0, 0, 0.1);
		border-radius: 3px;
		.searlist {
			padding: 0 25px;
			display: flex;
			justify-content: space-between;
			cursor: pointer;
			font-size: 14px;
			height: 40px;
			line-height: 40px;
			border-radius: 3px;
		}
		.searlist:hover {
			background: #f9fafd;
		}
	}
	.searchsleft{
	  left: 0%;
	}
	.searchsright{
	  left: 57%;
	}
}
</style>
