<template>
    <div class="flex w-full min-h-screen items-center justify-center">
        <form
            @submit.prevent="handleLogin"
            class="w-full max-w-sm rounded-lg bg-white p-8 shadow-md"
        >
            <h2 class="mb-6 text-center text-2xl font-bold text-gray-800">登录</h2>

            <div class="mb-4">
                <label class="mb-1 block text-sm font-medium text-gray-700">邮箱</label>
                <input
                    v-model="email"
                    type="email"
                    placeholder="请输入 用户名"
                    class="w-full rounded border border-gray-300 px-3 py-2 text-sm focus:border-blue-500 focus:outline-none"
                />
            </div>

            <div class="mb-6">
                <label class="mb-1 block text-sm font-medium text-gray-700">密码</label>
                <input
                    v-model="password"
                    type="password"
                    placeholder="请输入 密码"
                    class="w-full rounded border border-gray-300 px-3 py-2 text-sm focus:border-blue-500 focus:outline-none"
                />
            </div>

            <p v-if="error" class="mb-4 text-center text-sm text-red-500">{{ error }}</p>

            <button
                type="submit"
                :disabled="loading"
                class="w-full rounded bg-blue-600 py-2 text-sm font-medium text-white transition hover:bg-blue-700 disabled:cursor-not-allowed disabled:opacity-50"
            >
                {{ loading ? "登录中..." : "登录" }}
            </button>
        </form>
    </div>
</template>

<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";
import Cookies from "js-cookie";

const router = useRouter();

const email = ref("");
const password = ref("");
const error = ref("");
const loading = ref(false);

const apiClient = axios.create({
    baseURL: import.meta.env.VITE_API_BASEURL,
    headers: { "Content-Type": "application/json" },
});

async function handleLogin() {
    error.value = "";
    loading.value = true;

    try {
        const { data } = await apiClient.post("/login", {
            email: email.value,
            password: password.value,
        });

        if (data.ok && data.data?.token) {
            Cookies.set("token", data.data.token, { expires: 1 });
            router.push("/");
        } else {
            error.value = data.message || "登录失败，请重试";
        }
    } catch (err) {
        error.value = err.response?.data?.message || "网络错误，请稍后重试";
    } finally {
        loading.value = false;
    }
}
</script>
