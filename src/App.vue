<template>
    <div class="darkMode">
        <!--  
          헤더 영역
        -->
        <div class="border-t border-yellow-500 border-[60px] darkMode dark:border-[#9D6B15]"></div>
        <img src="../src/assets/images/logo.png" alt="logo" class="w-[150px] lg:w-[220px] bg-contain m-auto mt-7 my-4 dark:hidden">
        <img src="../src/assets/images/logo_dark2.png" alt="logo" class="w-[150px] lg:w-[220px] bg-contain m-auto mt-7 my-4 hidden dark:block">
        <h2 class="text-center font-extrabold text-4xl lg:text-5xl mt-4 dark:text-white">택배 조회</h2>
        <p class="text-center text-xs mb-3 lg:text-base dark:text-[#969696]">스마트 택배 조회 서비스</p>
        <!-- 
          바디 영역
        -->
        <div class=" darkMode bg-[#f7f8f9] dark:bg-[#252525] dark: w-4/5 md:w-3/5 xl:w-4/12 mx-auto flex rounded items-center pt-2 flex-wrap">
    
            <!-- 각각 국내, 국외 선택하면 대표 택배사를 자동으로 선택 -->
            <div class="border-b basis-full py-2 px-5 flex justify-center items-center text-sm">
                <span class="basis-[36%] tracking-[-0.13em] text-center mr-2 lg:mr-0 dark:text-[#f7f8f9]">국내 / 국외 선택</span> 
                <button @click="isBtn = 1; t_code='04';" class="text-sm text-white border p-1 px-5 rounded mr-2 bg-[#446DB2] dark:bg-[#20314D] hover:opacity-[0.7] duration-500 focus:opacity-[0.8]">국내</button>
                <button @click="isBtn = 2; t_code='12';" class="text-sm text-white border p-1 px-5 rounded ml-2 bg-[#446DB2] dark:bg-[#20314D] hover:opacity-[0.7] duration-500 focus:opacity-[0.8]">국외</button>
            </div>
            <div class="mt-5 w-full">
                <!-- 배송사 선택 -->
                <div class="text-center">
                    <select v-model="t_code" class="text-center py-2 bg-[#f7f8f9] dark:bg-[#252525] dark:text-white">
                  <option v-for="e in Company" :key="e" :value="e.Code">{{ e.Name }}</option>
                </select>
                </div>
                <!-- 운송장 번호 입력칸 -->
                <div class="text-center w-full">
                    <input id="t_invoice" v-model="t_invoice" @input="bindNumber" type="text" placeholder="운송장번호입력" class="border w-full py-4 mt-5 rounded-md outline-gray-300 text-center dark:bg-[#555] dark:caret-white dark:text-white">
                </div>
            </div>
    
            <!-- 조회하기 버튼 -->
            <div class="w-full text-center mb-3">
                <button @click="postListState(), PostList()" class="w-full bg-green-700 rounded-md text-white py-4 mt-8 mb-5 font-extrabold border hover:opacity-[0.7] duration-300 dark:bg-[#0A3229]">조회하기</button>
            </div>
        </div>
        <!-- 에러 메세지 -->
        <p class="text-center text-2xl text-red-500 font-bold animate-pulse">{{ errorMsg }}</p>
        <div class="window" v-if="isShow && errorMsg === ''">
            <!-- 
    
            데이터 출력값 / (운송장 번호, 택배사)
          
          -->
            <div class="">
                <div class="text-center flex justify-evenly">
                    <div class="border border-gray-300 rounded-full p-2 px-5 mt-5 bg-gray-200 mx-4 dark:bg-[#555] dark:text-white">택배사</div>
                    <div class="border border-gray-300 rounded-full p-2 px-5 mt-5 bg-gray-200 mx-4 dark:bg-[#555] dark:text-white">운송장 번호</div>
                </div>
                <div class="text-center flex justify-evenly mt-3">
                    <div class="mt-2 mb-5 mx-2 dark:text-white">{{ TrackingCode[0] }}</div>
                    <div class="mt-2 mb-5 mx-2 dark:text-white">{{ Trackings.invoiceNo }}</div>
                </div>
            </div>
    
            <!-- 이미지 데이터 바인딩 -->
            <div class="w-full mt-5 lg:mt-10 border-t-2">
                <div class="my-5 flex justify-evenly">
    
                    <!-- 마진 오토 확인하기 -->
                    <div class="relative mt-5 inline" v-for="level in 5" :key="level">
                        <img v-if="Trackings.level-1 === level" :src="require(`@/assets/images/ic_tropical_delivery_step${level}_on.png`)" />
                        <!-- 'off'일 경우 -->
                        <img v-else :src="require(`@/assets/images/ic_tropical_delivery_step${level}_off.png`)" class="mx-auto" />
                        <p v-if="Trackings.level-1 === level" :class="'mt-3 text-[#446DB2] dark:text-[#99BBF4] font-extrabold animate-pulse'">{{ PostlistName[level-1] }}</p>
                        <p v-else class="mt-3 text-center dark:text-white">{{ PostlistName[level-1] }}</p>
                    </div>
                </div>
            </div>
            <!-- 배송 상세 정보 -->
            <div class="py-5">
                <div v-for="(e, index) in Trackings.trackingDetails" :key="index" class="m-10 [&:last-child>div>p>span]:inline">
                    <p v-if="e.kind === Trackings.lastStateDetail.kind" class="dark:text-white">{{ e.where }} | <span class="text-[#446DB2] dark:text-[#99BBF4] font-bold animate-pulse">{{ Trackings.lastStateDetail.kind }}</span></p>
                    <p v-else class="dark:text-white">{{ e.where }} | <span class="text-gray-400">{{ e.kind }}</span></p>
                    <p class="mt-2 text-sm dark:text-white ">{{ e.telno }}</p>
                    <p class="mt-2 text-xs text-gray-500">{{ e.timeString }}</p>
                </div>
            </div>
        </div>
        <NavMenu :isDark="isDark" @dark="toggleDark()" />
    </div>
</template>

<script>

import axios from 'axios'
import { useDark, useToggle } from '@vueuse/core'
import NavMenu from './components/Nav.vue'

// import Tracking from './assets/Tracking.json'
// import CarrierList from './assets/Company.json'

export default {
    name: 'App',
    data() {
        return {
            isBtn: 1,
            t_key: process.env.VUE_APP_API_KEY,
            t_code: "04",
            t_invoice: "",
            Trackings: [],
            Carriers: [],
            errorMsg: '',
            PostlistName: ["상품인수", "상품이동중", "배송지도착", "배송출발", "배송완료"],
            isShow: 0,
            isDark: useDark(),
            toggleDark: useToggle(useDark()),
        }
    },
    created() {
        axios.get(`https://info.sweettracker.co.kr/api/v1/companylist?t_key=${process.env.VUE_APP_API_KEY}`)
            .then((res) => {
                this.Carriers = res.data.Company
                console.log(this.Carriers)
            })
            .catch((error) => {
                console.log(error);
            })
            
    },
    methods: {
        bindNumber() {
            return this.t_invoice = this.t_invoice.replace(/[^0-9]/g, '')

        },
        postListState() {
            return (this.ContentState === true ? this.ContentState = false : this.ContentState = true)
        },
        PostList() {
            // 
            // 포스트 리스트가 실행되기 전 조건문 실행
            // 
            // 데이터 입력 값이 없거나 맞지 않는 데이터값을 실행하면 에러메세지 발동
            // 만약에 송장번호값이 빈칸이라면 에러 메세지를 뜨게 작동
            if (this.t_invoice === '') {
                this.errorMsg = "운송장 번호를 입력해주세요."
                // .focus() - 커서 동작, 오직 하나만 사용 가능
                // ex) 아이디와 비번 입력칸 중, 둘 중 하나만 택1 가능
                document.querySelector("#t_invoice").focus()
                return
            }
            axios.get("https://info.sweettracker.co.kr/api/v1/trackingInfo", {
                params: {
                    t_code: this.t_code,
                    t_invoice: this.t_invoice,
                    t_key: this.t_key
                }
            }).then((res) => {
                console.log(res)
                if (res.data.code === '104') {
                    this.errorMsg = res.data.msg
                } else {
                    this.Trackings = res.data
                    // 혹시 모를 버그 때문에 작성 / 데이터 초기화 시켜줌
                    this.errorMsg = ''
                    this.isShow = true
                }
            }).catch((error) => {
                console.log(error)
            })
        },
        DarkMode() {
            document.querySelector("html").classList.toggle("dark")
        },
    },
    computed: {
        TrackingCode() {
            return this.Carriers.filter((e) => {
                return e.Code === this.t_code
            }).map(e => e.Name);
        },
        Company() {
            return this.Carriers.filter((data) => {
                if (this.isBtn == 1) {
                    return data.International == 'false'
                } else {
                    return data.International == 'true'
                }
            })
        }
    },
    components: {
        NavMenu
    },
    mounted() {
        // console.log(this.Trackings)
    },

}
</script>

<style>
* {
    font-family: 'GmarketSansMedium';
}
</style>
