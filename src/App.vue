<script setup>
import { ref } from 'vue';
import QRCode from 'qrcode';

const qrCode = ref({
	show: false,
	imageUrl: '',
	text: '',
});

const inputsStates = ref({
	loadingButton: false,
	userTextInput: '',
	rules: [(v) => v.length !== 0 || 'text input can not be EMPTY!'],
});

const form = ref();

const clickGenerateButton = () => {
	form.value.validate().then(({ valid }) => {
		if (valid === true) {
			inputsStates.value.loadingButton = true;
			generateQrCode();
		}
	});
};

const generateQrCode = () => {
	const options = {
		type: 'image/png',
		color: {
			dark: '#222831',
			light: '#eeeeee',
		},
		width: 500,
	};

	QRCode.toDataURL(inputsStates.value.userTextInput, options)
		.then((url) => {
			qrCode.value.text = inputsStates.value.userTextInput;
			qrCode.value.imageUrl = url;
			qrCode.value.show = true;
		})
		.catch((err) => {
			console.error(err);
		})
		.finally(() => {
			inputsStates.value.loadingButton = false;
		});
};

const downloadButtonLoading = ref(false);
const downloadQrCode = () => {
	downloadButtonLoading.value = true;
	const link = document.createElement('a');
	link.download = 'qrcode.jpg';
	link.href = qrCode.value.imageUrl;
	link.click();
	downloadButtonLoading.value = false;
};
</script>

<template>
	<header class="mb-10">
		<v-container>
			<v-row>
				<v-col cols="12" md="4" class="d-flex align-center">
					<div>
						<p class="text-body-1">Find Me By</p>
						<h1 class="ml-3 text-h4 logo">SHAKSTICK</h1>
					</div>
				</v-col>
				<v-col cols="12" md="4" class="d-flex justify-center align-center">
					<h2 class="text-h6">Qr Code Maker</h2>
				</v-col>
			</v-row>
		</v-container>
	</header>

	<main>
		<v-container>
			<v-card class="input-container rounded-lg">
				<v-form ref="form">
					<v-container>
						<v-row>
							<v-col cols="12" md="10">
								<v-text-field
									v-model="inputsStates.userTextInput"
									:rules="inputsStates.rules"
									variant="solo"
									placeholder="Enter a text or a website url to generate QR CODE"
								/>
							</v-col>
							<v-col cols="12" md="2">
								<v-btn
									@click.prevent="clickGenerateButton"
									:loading="inputsStates.loadingButton"
									type="submit"
									class="w-100 font-weight-bold text-body-1 generate-btn"
									color="#00adb5"
									>Generate</v-btn
								>
							</v-col>
						</v-row>
					</v-container>
				</v-form>
			</v-card>
			<v-row>
				<v-col
					v-if="qrCode.show"
					cols="12"
					class="d-flex justify-center align-center flex-column mt-5"
				>
					<pre>QR CODE for: {{ qrCode.text }}</pre>
					<img
						class="qr-code-image"
						:src="qrCode.imageUrl"
						alt="Qr Code Image"
					/>
					<v-btn
						@click="downloadQrCode"
						color="#ff2e63"
						:loading="downloadButtonLoading"
					>
						download QR Code
					</v-btn>
				</v-col>
			</v-row>
		</v-container>
	</main>
</template>

<style scoped>
header {
	background-color: var(--black-color);
	color: var(--white-color);
}

.logo {
	letter-spacing: 5px !important;
}

.qr-code-image {
	max-width: 350px;
	width: 75%;
}

@media (min-width: 960px) {
	.generate-btn {
		height: 72.5% !important;
	}
}
</style>
