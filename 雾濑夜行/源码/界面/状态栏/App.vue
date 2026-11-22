<template>
  <div class="hud" v-if="store.data">
    <div class="hud-header">
      <span class="hud-title">雾濑夜行 · 海雾监控终端</span>
      <span class="hud-meta">
        <span><b>{{ sys.日期 }}</b> {{ sys.时间 }}</span>
        <span>{{ sys.当前区域 }} · {{ sys.当前场景 || '—' }}</span>
        <span>{{ sys.天气 }}</span>
      </span>
    </div>

    <!-- 主角 -->
    <section class="sec">
      <button class="sec-head" :class="{ open: open.主角 }" @click="open.主角 = !open.主角">
        <span>主角 · {{ 主角.姓名 || 主角.身份 }}</span>
        <span class="arrow">▾</span>
      </button>
      <div class="sec-body" v-if="open.主角">
        <div class="stat-row"><span class="k">身份</span><span class="v">{{ 主角.身份 }}</span></div>
        <div class="stat-row"><span class="k">评级</span><span class="v">{{ 主角.评级 || '—' }}</span></div>
        <div class="stat-row">
          <span class="k">状态</span>
          <span class="v">
            <span class="badge" :class="状态badge(主角.状态)">{{ 主角.状态 }}</span>
          </span>
        </div>
        <div class="stat-row">
          <span class="k">外观</span>
          <span class="v">{{ 主角.外观 || '—' }}</span>
        </div>
        <div class="stat-row">
          <span class="k">体力</span>
          <span class="v">{{ 主角.体力 }}/100</span>
        </div>
        <div class="bar stamina"><i :style="{ width: clampPct(主角.体力, 100) + '%' }"></i></div>
        <div class="stat-row" style="margin-top:8px">
          <span class="k">元素能量</span>
          <span class="v">{{ 主角.元素能量.当前值 }}/{{ 主角.元素能量.上限 }}</span>
        </div>
        <div class="bar energy"><i :style="{ width: clampPct(主角.元素能量.当前值, 主角.元素能量.上限 || 1) + '%' }"></i></div>
        <div class="stat-row" style="margin-top:8px">
          <span class="k">阵营倾向</span>
          <span class="v">
            <span class="badge" :class="阵营badge(主角.阵营倾向)">{{ 阵营label(主角.阵营倾向) }}</span>
          </span>
        </div>
        <div class="bar affinity"><i :style="{ width: affinityPct(主角.阵营倾向) + '%' }"></i></div>
      </div>
    </section>

    <!-- 性开发 -->
    <section class="sec">
      <button class="sec-head" :class="{ open: open.性开发 }" @click="open.性开发 = !open.性开发">
        <span>NSFW · 性开发面板</span>
        <span class="arrow">▾</span>
      </button>
      <div class="sec-body" v-if="open.性开发">
        <div class="organ-grid">
          <div class="organ" v-for="(v, k) in 性开发" :key="k">
            <div class="o-name">{{ k }}</div>
            <div class="o-line"><span>开发</span> {{ v.开发程度 }}</div>
            <div class="o-line"><span>次数</span> {{ v.使用次数 }}</div>
            <div class="o-line"><span>初次</span> {{ v.初次性对象 }}</div>
            <div class="o-line"><span>最近</span> {{ v.最近性对象 }}</div>
          </div>
        </div>
      </div>
    </section>

    <!-- 传闻与线索 -->
    <section class="sec">
      <button class="sec-head" :class="{ open: open.传闻与线索 }" @click="open.传闻与线索 = !open.传闻与线索">
        <span>传闻与线索（{{ (sys.传闻与线索 || []).length }}）</span>
        <span class="arrow">▾</span>
      </button>
      <div class="sec-body" v-if="open.传闻与线索">
        <div class="clue-list" v-if="(sys.传闻与线索 || []).length">
          <div class="clue" v-for="(c, i) in sys.传闻与线索" :key="i">{{ c }}</div>
        </div>
        <div class="empty" v-else>暂无传闻与线索</div>
      </div>
    </section>

    <!-- 在场 -->
    <section class="sec">
      <button class="sec-head" :class="{ open: open.在场 }" @click="open.在场 = !open.在场">
        <span>在场角色</span>
        <span class="arrow">▾</span>
      </button>
      <div class="sec-body" v-if="open.在场">
        <div class="grid-2" v-if="在场列表.length">
          <div class="card" v-for="[k, v] in 在场列表" :key="k">
            <div class="name">{{ v.名 }}</div>
            <div class="stat-row"><span class="k">好感</span><span class="v">{{ v.好感 }}</span></div>
            <div class="stat-row"><span class="k">性欲</span><span class="v">{{ v.性欲 }}</span></div>
            <div class="stat-row"><span class="k">外观</span><span class="v">{{ v.外观 || '—' }}</span></div>
            <div class="stat-row"><span class="k">心事</span><span class="v">{{ v.心事 || '—' }}</span></div>
            <div class="stat-row"><span class="k">身体</span><span class="v">{{ v.身体 || '—' }}</span></div>
          </div>
        </div>
        <div class="empty" v-else>暂无在场角色</div>
      </div>
    </section>

    <!-- 关系 -->
    <section class="sec">
      <button class="sec-head" :class="{ open: open.关系 }" @click="open.关系 = !open.关系">
        <span>关系追踪（{{ 关系列表.length }}）</span>
        <span class="arrow">▾</span>
      </button>
      <div class="sec-body" v-if="open.关系">
        <div class="grid-2" v-if="关系列表.length">
          <div class="card" v-for="[name, v] in 关系列表" :key="name">
            <div class="name">{{ name }}</div>
            <div class="stat-row"><span class="k">关系</span><span class="v">{{ v.关系 }}</span></div>
            <div class="stat-row"><span class="k">好感</span><span class="v">{{ v.好感度 }}</span></div>
            <div class="stat-row"><span class="k">性欲</span><span class="v">{{ v.性欲 }}</span></div>
            <div class="stat-row"><span class="k">体力</span><span class="v">{{ v.体力 }}</span></div>
            <div class="stat-row" v-if="v.元素能量 && v.元素能量.上限 > 0"><span class="k">元素</span><span class="v">{{ v.元素能量.当前值 }}/{{ v.元素能量.上限 }}</span></div>
            <div class="stat-row" v-if="v.性行为次数 > 0"><span class="k">性行为</span><span class="v">{{ v.性行为次数 }} 次</span></div>
            <div class="stat-row" v-if="v.初次性对象 && v.初次性对象 !== '未知'"><span class="k">初次对象</span><span class="v">{{ v.初次性对象 }}</span></div>
            <div class="stat-row" v-if="v.最近性对象 && v.最近性对象 !== '未知'"><span class="k">最近对象</span><span class="v">{{ v.最近性对象 }}</span></div>
          </div>
        </div>
        <div class="empty" v-else>暂无关系记录</div>
      </div>
    </section>
  </div>
</template>

<script setup lang="ts">
import { computed, reactive } from 'vue';
import { useDataStore } from './store';

const store = useDataStore();

const sys = computed(() => store.data?.系统 ?? {});
const 主角 = computed(() => store.data?.主角 ?? {});
const 性开发 = computed(() => store.data?.主角?.性开发 ?? {});
const 在场列表 = computed(() => Object.entries(store.data?.在场 ?? {}).filter(([, v]) => v && (v as any).名));
const 关系列表 = computed(() => Object.entries(store.data?.关系 ?? {}));

const open = reactive<Record<string, boolean>>({ 主角: true, 性开发: false, 传闻与线索: false, 在场: false, 关系: false });

function clampPct(v: number, max: number) {
  if (!max) return 0;
  return Math.max(0, Math.min(100, (v / max) * 100));
}

function affinityPct(v: number) {
  return Math.max(0, Math.min(100, ((v + 100) / 200) * 100));
}

function 阵营label(v: number) {
  if (v > 20) return '亲人类 ' + v;
  if (v < -20) return '亲妖魔 ' + v;
  return '中立 ' + v;
}

function 阵营badge(v: number) {
  if (v > 20) return '';
  if (v < -20) return 'danger';
  return 'neutral';
}

function 状态badge(v: string) {
  if (v === '健康') return '';
  if (v === '重伤') return 'danger';
  return 'neutral';
}
</script>
