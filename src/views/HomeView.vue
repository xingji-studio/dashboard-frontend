<template>
    <div class="flex w-full min-h-screen items-center justify-center">
        <div class="w-full max-w-sm rounded-lg bg-white p-8 shadow-md">
            <h2 class="mb-6 text-center text-2xl font-bold text-gray-800">个人信息</h2>
            <p v-if="loading" class="text-center text-sm text-gray-500">加载中...</p>
            <p v-if="error" class="mb-4 text-center text-sm text-red-500">{{ error }}</p>
            <div v-if="user" class="space-y-3">
                <div>
                    <label class="mb-1 block text-sm font-medium text-gray-700">邮箱</label>
                    <p class="rounded border border-gray-300 px-3 py-2 text-sm">{{ user.email }}</p>
                </div>
                <div>
                    <label class="mb-1 block text-sm font-medium text-gray-700">姓名</label>
                    <p class="rounded border border-gray-300 px-3 py-2 text-sm">{{ user.username }}</p>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";
import Cookies from "js-cookie";

const router = useRouter();

const user = ref(null);
const error = ref("");
const loading = ref(true);

const apiClient = axios.create({
    baseURL: import.meta.env.VITE_API_BASEURL,
    headers: { "Content-Type": "application/json" },
});

onMounted(async () => {
    const token = Cookies.get("token");
    if (!token) {
        router.replace("/login");
        return;
    }

    try {
        const payload = JSON.parse(atob(token.split(".")[1]));
        if (payload.exp && Date.now() > payload.exp * 1000) {
            router.replace("/login");
            return;
        }
    } catch {
        router.replace("/login");
        return;
    }

    try {
        const { data } = await apiClient.get("/profile", {
            headers: { Authorization: `Bearer ${token}` },
        });
        if (data.ok && data.data) {
            user.value = data.data;
        } else {
            error.value = data.message || "获取信息失败";
        }
    } catch (err) {
        if (err.response?.status === 401 || err.response?.status === 403) {
            router.replace("/login");
            return;
        }
        error.value = err.response?.data?.message || "网络错误，请稍后重试";
    } finally {
        loading.value = false;
    }
});
</script>
