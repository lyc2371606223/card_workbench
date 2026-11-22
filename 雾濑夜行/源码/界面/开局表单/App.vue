<template>
  <div class="form-wrap">
    <div class="form-head">自建巫女 · 雾濑站前入卡</div>
    <p class="form-sub">填写角色信息后进入雾濑市，其余可边玩边补。</p>

    <label class="field">
      <span>姓名</span>
      <input v-model="姓名" type="text" placeholder="例：雾岛小夜" />
    </label>

    <label class="field">
      <span>元素倾向</span>
      <select v-model="元素倾向">
        <option>风</option>
        <option>光·水</option>
        <option>暗</option>
        <option>金·火</option>
        <option>全元素均衡</option>
        <option>无异能（发明家）</option>
      </select>
    </label>

    <label class="field">
      <span>出身</span>
      <select v-model="出身">
        <option>稻荷神社</option>
        <option>神田神社</option>
        <option>天狐神社</option>
        <option>自由巫女（不隶属神社）</option>
        <option>被驱逐的独立巫女</option>
        <option>野生觉醒</option>
      </select>
    </label>

    <label class="field">
      <span>战力评级</span>
      <select v-model="评级">
        <option>丁下</option>
        <option>丁上</option>
        <option>丙下</option>
        <option>丙上</option>
        <option>乙下</option>
        <option>甲下</option>
        <option>无异能</option>
      </select>
    </label>

    <button class="submit" :disabled="!姓名.trim()" @click="提交">进入雾濑市</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { createChatMessages } from '@types/function/chat_message';
import { triggerSlash } from '@types/function/slash';
import { useDataStore } from '../store';

const store = useDataStore();

const 姓名 = ref('');
const 元素倾向 = ref('风');
const 出身 = ref('稻荷神社');
const 评级 = ref('丁下');

const 评级能量上限: Record<string, number> = {
  丁下: 30,
  丁上: 50,
  丙下: 70,
  丙上: 90,
  乙下: 110,
  甲下: 160,
  无异能: 0,
};

const 评级战力: Record<string, string> = {
  丁下: '丁下',
  丁上: '丁上',
  丙下: '丙下',
  丙上: '丙上',
  乙下: '乙下',
  甲下: '甲下',
  无异能: '无异能',
};

async function 提交() {
  const name = 姓名.value.trim();
  const upper = 评级能量上限[评级.value] ?? 30;

  store.data.主角.身份 = '自定义';
  store.data.主角.姓名 = name;
  store.data.主角.评级 = 评级战力[评级.value] ?? 评级.value;
  store.data.主角.状态 = '健康';
  store.data.主角.体力 = 100;
  store.data.主角.阵营倾向 = 60;
  store.data.主角.元素能量 = { 上限: upper, 当前值: upper };
  store.data.主角.性开发 = {
    口: { 开发程度: '未开发', 使用次数: 0, 初次性对象: '未知', 最近性对象: '未知' },
    穴: { 开发程度: '未开发', 使用次数: 0, 初次性对象: '未知', 最近性对象: '未知' },
    肛: { 开发程度: '未开发', 使用次数: 0, 初次性对象: '未知', 最近性对象: '未知' },
    手: { 开发程度: '未开发', 使用次数: 0, 初次性对象: '未知', 最近性对象: '未知' },
    足: { 开发程度: '未开发', 使用次数: 0, 初次性对象: '未知', 最近性对象: '未知' },
    胸: { 开发程度: '未开发', 使用次数: 0, 初次性对象: '未知', 最近性对象: '未知' },
  };
  store.data.系统.当前区域 = '商业区';
  store.data.系统.当前场景 = '雾濑站前';
  store.data.系统.剧情阶段 = '日常';

  const desc = `我叫${name}，${元素倾向.value}元素，出身${出身.value}，评级${评级战力[评级.value]}。刚搭列车到雾濑站前，海雾正浓。`;

  createChatMessages([{ role: 'user', name: name, message: desc }]).then(() => {
    triggerSlash('/trigger');
  });
}
</script>

<style scoped>
.form-wrap {
  width: 100%;
  max-width: 420px;
  margin: 0 auto;
  background: #0b0d16;
  border: 1px solid rgba(63, 208, 212, 0.22);
  border-radius: 8px;
  padding: 16px;
  color: #d9e0e6;
  font-family: 'Sarasa UI SC', 'Noto Sans SC', sans-serif;
  font-size: 13px;
}

.form-head {
  font-size: 15px;
  letter-spacing: 1px;
  color: #3fd0d4;
  margin-bottom: 4px;
}

.form-sub {
  color: #8a93a3;
  font-size: 12px;
  margin-bottom: 14px;
}

.field {
  display: flex;
  flex-direction: column;
  gap: 4px;
  margin-bottom: 12px;
}

.field span {
  color: #8a93a3;
  font-size: 12px;
}

.field input,
.field select {
  background: #12141f;
  border: 1px solid rgba(138, 147, 163, 0.3);
  border-radius: 5px;
  color: #d9e0e6;
  padding: 8px 10px;
  font-size: 13px;
  outline: none;
}

.field input:focus,
.field select:focus {
  border-color: #3fd0d4;
}

.submit {
  width: 100%;
  background: linear-gradient(90deg, rgba(63, 208, 212, 0.25), rgba(212, 80, 92, 0.2));
  border: 1px solid rgba(63, 208, 212, 0.4);
  color: #eaf4f5;
  border-radius: 5px;
  padding: 10px;
  cursor: pointer;
  font-size: 14px;
  letter-spacing: 2px;
  transition: background 0.15s;
}

.submit:hover:not(:disabled) {
  background: rgba(63, 208, 212, 0.35);
}

.submit:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>
