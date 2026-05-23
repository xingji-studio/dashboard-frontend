<template>
    <form @submit.prevent="handleLogin" class="w-full max-w-sm rounded-lg bg-white p-8 shadow-md">
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
            class="w-full rounded bg-blue-600 py-2 text-sm font-medium text-white transition hover:bg-blue-700"
        >
            登录
        </button>
    </form>
</template>

<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();

const email = ref("");
const password = ref("");
const error = ref("");

function handleLogin() {
    if (email.value === "test@example.com" && password.value === "123456") {
        error.value = "";
        router.push("/");
    } else {
        error.value = "邮箱或密码错误";
    }
}
</script>
