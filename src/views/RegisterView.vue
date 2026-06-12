<template>
    <div class="bg-cover bg-center w-full" style="background-image: url('/login_bg.jpg');">
        <div class="min-h-screen flex items-center justify-center p-4 pt-20">
            <div class="w-full max-w-md bg-gray-800 opacity-90 rounded-lg shadow-xl overflow-hidden">
                <div class="bg-[#1da1f2] py-8 px-6">
                    <h2 class="text-3xl font-bold text-white text-center">注册</h2>
                    <p class="text-blue-100 text-center mt-2">创建一个 XINGJI 账号并开启您的星际之旅！</p>
                </div>

                <div class="py-8 px-6">

                    <form @submit.prevent="handleRegister">

                        <div class="mb-4">
                            <label class="block text-gray-300 text-sm font-medium mb-1">用户名</label>
                            <input
                                v-model="username"
                                type="text"
                                placeholder="请输入 用户名"
                                class="w-full px-4 py-3 bg-gray-700 border border-gray-600 rounded-md text-white focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                            />
                        </div>

                        <div class="mb-4">
                            <label class="block text-gray-300 text-sm font-medium mb-1">邮箱</label>
                            <input
                                v-model="email"
                                type="text"
                                placeholder="请输入 邮箱"
                                class="w-full px-4 py-3 bg-gray-700 border border-gray-600 rounded-md text-white focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                            />
                        </div>

                        <div class="mb-6">
                            <label class="block text-gray-300 text-sm font-medium mb-1">密码</label>
                            <input
                                v-model="password"
                                type="text"
                                placeholder="请输入 密码"
                                class="w-full px-4 py-3 bg-gray-700 border border-gray-600 rounded-md text-white focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                            />
                        </div>

                        <p v-if="error" class="mb-4 text-center text-sm text-red-500">{{ error }}</p>
                        <p v-if="successMsg" class="mb-4 text-center text-sm text-green-500">
                            {{ successMsg }}
                        </p>

                        <button
                            type="submit"
                            :disabled="loading"
                            class="w-full bg-[#1da1f2] hover:bg-[#1da1f2] text-white font-medium py-3 px-4 rounded-md transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 focus:ring-offset-gray-800 disabled:bg-blue-500 disabled:cursor-not-allowed"
                        >
                            {{ loading ? "注册中..." : "注册" }}
                        </button>
                    </form>
                    <div class="text-center mt-6">
                        <p class="text-gray-400 text-sm">
                        已有账号？
                        <router-link :to="redirect ? `/login?redirect=${encodeURIComponent(redirect)}` : '/login'" class="text-blue-400 hover:text-blue-300 transition-colors duration-200">
                            立即登录
                        </router-link>
                        </p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";

const router = useRouter();

const username = ref("");
const email = ref("");
const password = ref("");
const error = ref("");
const successMsg = ref("");
const loading = ref(false);

const apiClient = axios.create({
    baseURL: import.meta.env.VITE_API_BASEURL,
    headers: { "Content-Type": "application/json" },
});

async function handleRegister() {
    error.value = "";
    successMsg.value = "";
    loading.value = true;

    try {
        const { data } = await apiClient.post("/register", {
            username: username.value,
            password: password.value,
            email: email.value,
        });

        if (data.ok && data.message?) {
            successMsg.value = data.message;
            setTimeout(() => router.push("/login"), 1500);
        } else {
            error.value = data.message || "注册失败，请重试";
        }
    } catch (err) {
        error.value = err.response?.data?.message || "网络错误，请稍后重试";
    } finally {
        loading.value = false;
    }
}
</script>
