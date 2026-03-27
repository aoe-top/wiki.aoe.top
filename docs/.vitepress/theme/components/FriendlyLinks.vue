<script setup>
import { onMounted, ref } from 'vue';

const links = ref([]);
const pending = ref(true);
const failed = ref(false);

async function loadLinks() {
    try {
        const response = await fetch('https://api.aoe.top/api/friendly/links');
        if (!response.ok) {
            throw new Error(`Failed to load links: ${response.status}`);
        }
        const data = await response.json();
        links.value = Array.isArray(data) ? data : [];
    } catch (error) {
        console.error(error);
        failed.value = true;
    } finally {
        pending.value = false;
    }
}

onMounted(loadLinks);
</script>

<template>
    <section class="friendly-links-shell">
        <div class="friendly-links-card">
            <div class="friendly-links-header">
                <p class="friendly-links-eyebrow">友情链接</p>
                <h2>和小莫一起折腾的站点</h2>
                <p>
                    实时读取最新友链列表，保持维基底部的跳转入口始终同步。
                </p>
            </div>
            <div v-if="pending" class="friendly-links-state">友链加载中...</div>
            <div v-else-if="failed" class="friendly-links-state">
                友链暂时不可用，请稍后再试。
            </div>
            <div v-else class="friendly-links-grid">
                <a
                    v-for="link in links"
                    :key="`${link.name}-${link.url}`"
                    :href="link.url"
                    target="_blank"
                    rel="noopener noreferrer"
                    class="friendly-links-item"
                >
                    <span class="friendly-links-name">{{ link.name }}</span>
                    <span v-if="link.description" class="friendly-links-desc">{{ link.description }}</span>
                </a>
            </div>
        </div>
    </section>
</template>

<style scoped>
.friendly-links-shell {
    padding: 0 24px 32px;
}

.friendly-links-card {
    max-width: 1152px;
    margin: 0 auto;
    padding: 28px;
    border: 1px solid rgba(95, 103, 238, 0.18);
    border-radius: 24px;
    background:
        radial-gradient(circle at top right, rgba(95, 103, 238, 0.18), transparent 32%),
        linear-gradient(180deg, rgba(21, 23, 38, 0.92), rgba(14, 16, 28, 0.96));
    box-shadow: 0 24px 60px rgba(8, 10, 20, 0.28);
}

.friendly-links-header h2 {
    margin: 8px 0 10px;
    font-size: 1.6rem;
    color: #f3f4ff;
}

.friendly-links-header p {
    margin: 0;
    color: rgba(226, 229, 255, 0.72);
    line-height: 1.7;
}

.friendly-links-eyebrow {
    letter-spacing: 0.18em;
    font-size: 0.75rem;
    text-transform: uppercase;
    color: #aeb6ff;
}

.friendly-links-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
    gap: 14px;
    margin-top: 22px;
}

.friendly-links-item {
    display: block;
    padding: 14px 16px;
    border-radius: 18px;
    text-decoration: none;
    border: 1px solid rgba(255, 255, 255, 0.08);
    background: rgba(255, 255, 255, 0.04);
    transition: transform 0.2s ease, border-color 0.2s ease, background 0.2s ease;
}

.friendly-links-item:hover {
    transform: translateY(-2px);
    border-color: rgba(144, 154, 255, 0.48);
    background: rgba(95, 103, 238, 0.12);
}

.friendly-links-name {
    display: block;
    color: #f8f9ff;
    font-weight: 600;
}

.friendly-links-desc {
    display: block;
    margin-top: 6px;
    color: rgba(226, 229, 255, 0.58);
    font-size: 0.86rem;
    line-height: 1.5;
}

.friendly-links-state {
    margin-top: 20px;
    padding: 16px 18px;
    border-radius: 16px;
    background: rgba(255, 255, 255, 0.04);
    color: rgba(226, 229, 255, 0.72);
}

@media (max-width: 640px) {
    .friendly-links-shell {
        padding: 0 16px 24px;
    }

    .friendly-links-card {
        padding: 22px;
        border-radius: 20px;
    }
}
</style>