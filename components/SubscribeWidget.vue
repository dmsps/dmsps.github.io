<template>
    <div class="subscribe">
        <el-popover placement="top" width="320" v-model="isVisible" @hide="toggleVisible(true)">
            <div>
                <template v-if="!isFormSended">
                    <h3 style="color: black; margin-bottom: 10px">
                        Отправим предожение на почту 🤟
                    </h3>
                    <el-form
                        :model="ruleForm"
                        :rules="rules"
                        ref="ruleForm"
                        :hide-required-asterisk="true"
                        size="small"
                    >
                        <el-form-item label="Email" prop="email">
                            <el-input v-model="ruleForm.email" size="medium"></el-input>
                        </el-form-item>
                        <el-form-item style="text-align: right">
                            <el-button size="small" type="text" @click="toggleVisible(false)">
                                Еще подумаю
                            </el-button>
                            <el-button size="small" type="primary" @click="submitForm('ruleForm')">
                                Отправить
                            </el-button>
                        </el-form-item>
                    </el-form>
                </template>
                <h3 v-else style="color: black">
                    Спасибо, наши менеджеры свяжутся с вами в ближайшее время
                </h3>
            </div>
            <button
                slot="reference"
                type="button"
                @mouseenter="toggleHover"
                @mouseleave="toggleHover"
                :class="['subscribe__wrapper', { active: isHovered }]"
            >
                <i style="font-size: 26px" class="el-icon-message subscribe__icon"></i>
                <div class="subscribe__title">Заказать демо</div>
            </button>
        </el-popover>
    </div>
</template>
<script>
export default {
    name: "SubscribeWidget",
    data() {
        return {
            isVisible: false,
            isHovered: false,
            isFormSended: false,
            ruleForm: {
                email: "",
            },
            rules: {
                email: [
                    { required: true, message: "Укажите email", trigger: "blur" },
                    { type: "email", message: "Некорректный email", trigger: "blur" },
                ],
            },
        }
    },
    methods: {
        toggleHover() {
            if (this.isVisible) return
            this.isHovered = !this.isHovered
        },
        toggleVisible(force) {
            if (!force) {
                this.isVisible = !this.isVisible
            }
            if (this.isHovered) {
                this.toggleHover()
            }
        },
        submitForm(formName) {
            this.$refs[formName].validate(async valid => {
                if (valid) {
                    // location.href = `mailto:join@deobytes.ru?subject=${this.ruleForm.email} заказываю демо-доступ в Deobytes`
                    const data = await this.$axios.post("/send.php", {
                        email: this.ruleForm.email,
                    })

                    if (data) {
                        this.isFormSended = true
                    }
                }
            })
        },
        clear() {
            this.ruleForm.email = ""
        },
    },
}
</script>
