<script setup>
import { ref } from 'vue';
import { useField, useForm } from 'vee-validate';
import * as yup from 'yup';
import InputField from '../components/InputField.vue';
import DatePicker from '../components/DatePicker.vue';
import CheckboxGroup from '../components/CheckboxGroup.vue';
import RadioGroup from '../components/RadioGroup.vue';
import FileUpload from '../components/FileUpload.vue';
import WorkExperience from '../components/WorkExperience.vue';
import ProjectField from '../components/ProjectField.vue';
import { VueRecaptchaV2 } from 'vue-recaptcha-v3';

// Список дозволених країн для поля державність
const allowedCountries = ['Україна', 'США', 'Канада', 'Німеччина', 'Індія'];

// Підтримувані формати файлів
const fileFormats = ['image/jpeg', 'image/png', 'application/pdf'];

const fileSchema = yup
    .mixed()
    .test('fileFormat', 'Невірний формат файлу', (value) => {
      if (!value) return true; // Якщо файл не завантажено, пропустити валідацію
      return Array.from(value).every((file) => fileFormats.includes(file.type));
    })
    .test('fileSize', 'Файл занадто великий (макс. 5МБ)', (value) => {
      if (!value) return true;
      return Array.from(value).every((file) => file.size <= 5242880); // 5MB
    });

const formSchema = yup.object({
  // Особисті дані
  firstName: yup.string().required("Ім'я є обов'язковим").min(2, "Ім'я повинно містити принаймні 2 символи"),
  lastName: yup.string().required("Прізвище є обов'язковим").min(2, "Прізвище повинно містити принаймні 2 символи"),
  birthDate: yup
      .date()
      .required("Дата народження є обов'язковою")
      .max(new Date(), "Дата народження повинна бути в минулому"),
  email: yup.string().email('Введіть коректну електронну пошту').required("Електронна пошта є обов'язковою"),
  phone: yup
      .string()
      .matches(/^\+?\d{10,15}$/, 'Номер телефону повинен містити від 10 до 15 цифр')
      .required('Номер телефону є обов’язковим'),
  address: yup.string().required("Адреса є обов'язковою").min(5, 'Адреса повинна бути довшою'),
  gender: yup.string().required('Поле "Стать" є обов’язковим'),
  maritalStatus: yup.string().required('Поле "Сімейний стан" є обов’язковим'),
  nationality: yup
      .string()
      .oneOf(allowedCountries, 'Оберіть коректну країну')
      .required('Державність є обов’язковою'),
  relocation: yup.array().min(1, 'Виберіть варіант готовності до переїзду'),

  // Освіта
  educationLevel: yup.string().required('Рівень освіти є обов’язковим'),
  specialization: yup.string().required('Спеціальність є обов’язковою'),
  institution: yup.string().required('Навчальний заклад є обов’язковим'),
  graduationYear: yup
      .number()
      .typeError('Введіть коректний рік закінчення')
      .min(1900, 'Рік закінчення повинен бути більше 1900')
      .max(new Date().getFullYear(), 'Рік закінчення не може бути в майбутньому'),
  certificates: yup.array().min(1, 'Виберіть хоча б один сертифікат'),

  // Професійний досвід
  experiences: yup.array().of(
      yup.object({
        position: yup.string().required('Посада є обов’язковою'),
        company: yup.string().required('Компанія є обов’язковою'),
        startDate: yup.date().required('Дата початку є обов’язковою'),
        endDate: yup
            .date()
            .min(yup.ref('startDate'), 'Дата завершення не може бути раніше дати початку')
            .required('Дата завершення є обов’язковою'),
      })
  ),

  // Мови
  nativeLanguage: yup.string().required('Рідна мова є обов’язковою'),
  languages: yup.array().min(1, 'Виберіть хоча б одну іноземну мову'),
  englishLevel: yup.string().required('Оберіть рівень володіння англійською'),

  // Навички
  professionalSkills: yup.string().required('Професійні навички є обов’язковими'),
  leadership: yup.string().required('Оцініть свої лідерські якості'),
  teamwork: yup.string().required('Оцініть свої навички роботи в команді'),
  softwareSkills: yup.array().min(1, 'Оберіть хоча б одну програму'),
  driversLicense: yup.string().required('Поле "Водійські права" є обов’язковим'),

  // Додаткові поля
  businessTrips: yup.array().min(1, 'Оберіть готовність до відряджень'),
  vehicle: yup.array().min(1, 'Оберіть наявність власного транспорту'),
  salary: yup
      .number()
      .typeError('Введіть коректний рівень заробітної плати')
      .positive('Заробітна плата повинна бути більше 0')
      .required('Поле "Заробітна плата" є обов’язковим'),
  overtime: yup.array().min(1, 'Оберіть можливість працювати понаднормово'),
  additionalSkills: yup.string().required('Поле "Додаткові навички" є обов’язковим'),

  // Завантаження файлів
  photo: fileSchema,
  resume: fileSchema.required('Завантажте резюме'),
  coverLetter: fileSchema,
  certificatesUpload: fileSchema,
  portfolio: fileSchema,

  // Проекти
  projects: yup.array().of(
      yup.object({
        name: yup.string().required('Назва проекту є обов’язковою'),
        description: yup.string().required('Опис проекту є обов’язковим'),
        role: yup.string().required('Ваша роль у проекті є обов’язковою'),
        link: yup
            .string()
            .url('Введіть коректне посилання')
            .required('Посилання на проект є обов’язковим'),
      })
  ),

  // Соціальні мережі
  linkedinProfile: yup.string().url('Введіть коректне посилання на LinkedIn').required('Посилання на LinkedIn є обов’язковим'),
  githubProfile: yup.string().url('Введіть коректне посилання на GitHub').required('Посилання на GitHub є обов’язковим'),
  facebookProfile: yup.string().url('Введіть коректне посилання на Facebook'),
  otherProfiles: yup.string().url('Введіть коректне посилання на інші профілі'),

  // reCAPTCHA
  recaptcha: yup.string().required('Підтвердіть, що ви не робот'),
});

// Підключаємо useForm
const { handleSubmit } = useForm({
  validationSchema: formSchema,
});

// Використовуємо useField для кожного поля
const { value: firstName, errorMessage: firstNameError } = useField('firstName');
const { value: lastName, errorMessage: lastNameError } = useField('lastName');
const { value: birthDate, errorMessage: birthDateError } = useField('birthDate');
const { value: email, errorMessage: emailError } = useField('email');
const { value: phone, errorMessage: phoneError } = useField('phone');
const { value: address, errorMessage: addressError } = useField('address');
const { value: gender, errorMessage: genderError } = useField('gender');
const { value: maritalStatus, errorMessage: maritalStatusError } = useField('maritalStatus');
const { value: nationality, errorMessage: nationalityError } = useField('nationality');
const { value: relocation, errorMessage: relocationError } = useField('relocation');
const { value: educationLevel, errorMessage: educationLevelError } = useField('educationLevel');
const { value: specialization, errorMessage: specializationError } = useField('specialization');
const { value: institution, errorMessage: institutionError } = useField('institution');
const { value: graduationYear, errorMessage: graduationYearError } = useField('graduationYear');
const { value: certificates, errorMessage: certificatesError } = useField('certificates');
const { value: nativeLanguage, errorMessage: nativeLanguageError } = useField('nativeLanguage');
const { value: languages, errorMessage: languagesError } = useField('languages');
const { value: englishLevel, errorMessage: englishLevelError } = useField('englishLevel');
const { value: professionalSkills, errorMessage: professionalSkillsError } = useField('professionalSkills');
const { value: leadership, errorMessage: leadershipError } = useField('leadership');
const { value: teamwork, errorMessage: teamworkError } = useField('teamwork');
const { value: softwareSkills, errorMessage: softwareSkillsError } = useField('softwareSkills');
const { value: driversLicense, errorMessage: driversLicenseError } = useField('driversLicense');
const { value: businessTrips, errorMessage: businessTripsError } = useField('businessTrips');
const { value: vehicle, errorMessage: vehicleError } = useField('vehicle');
const { value: salary, errorMessage: salaryError } = useField('salary');
const { value: overtime, errorMessage: overtimeError } = useField('overtime');
const { value: additionalSkills, errorMessage: additionalSkillsError } = useField('additionalSkills');
const { value: linkedinProfile, errorMessage: linkedinProfileError } = useField('linkedinProfile');
const { value: githubProfile, errorMessage: githubProfileError } = useField('githubProfile');
const { value: facebookProfile, errorMessage: facebookProfileError } = useField('facebookProfile');
const { value: otherProfiles, errorMessage: otherProfilesError } = useField('otherProfiles');

const submitForm = handleSubmit((values) => {
  console.log('Form submitted:', values);
});

const updatePhoto = (files) => {
  form.value.photo = files;
};

const updateResume = (files) => {
  form.value.resume = files;
};

const updateCoverLetter = (files) => {
  form.value.coverLetter = files;
};

const updateCertificates = (files) => {
  form.value.certificatesUpload = files;
};

const updatePortfolio = (files) => {
  form.value.portfolio = files;
};

const onVerify = (response) => {
  console.log('Recaptcha verified:', response);
};
</script>

<template>
  <form @submit.prevent="submitForm">
    <!-- Особисті дані -->
    <InputField :modelValue="firstName" label="Ім'я" id="first-name" :error="firstNameError" @update:modelValue="firstName = $event" />
    <InputField :modelValue="lastName" label="Прізвище" id="last-name" :error="lastNameError" @update:modelValue="lastName = $event" />
    <DatePicker :modelValue="birthDate" label="Дата народження" id="birth-date" :error="birthDateError" @update:modelValue="birthDate = $event" />
    <InputField :modelValue="email" label="Електронна пошта" id="email" :error="emailError" @update:modelValue="email = $event" />
    <InputField :modelValue="phone" label="Номер телефону" id="phone" :error="phoneError" @update:modelValue="phone = $event" />
    <InputField :modelValue="address" label="Адреса" id="address" :error="addressError" @update:modelValue="address = $event" />
    <RadioGroup :modelValue="gender" label="Стать" :options="['Чоловік', 'Жінка', 'Не вказано']" id="gender" :error="genderError" @update:modelValue="gender = $event" />
    <RadioGroup :modelValue="maritalStatus" label="Сімейний стан" :options="['Не у шлюбі', 'У шлюбі', 'Розлучений(а)']" id="maritalStatus" :error="maritalStatusError" @update:modelValue="maritalStatus = $event" />
    <InputField :modelValue="nationality" label="Державність" id="nationality" :error="nationalityError" @update:modelValue="nationality = $event" />
    <CheckboxGroup :modelValue="relocation" label="Готовність до переїзду" :options="['Так', 'Ні']" :error="relocationError" @update:modelValue="relocation = $event" />

    <!-- Освіта -->
    <RadioGroup :modelValue="educationLevel" label="Рівень освіти" :options="['Бакалавр', 'Магістр', 'Доктор наук']" id="educationLevel" :error="educationLevelError" @update:modelValue="educationLevel = $event" />
    <InputField :modelValue="specialization" label="Спеціальність" id="specialization" :error="specializationError" @update:modelValue="specialization = $event" />
    <InputField :modelValue="institution" label="Навчальний заклад" id="institution" :error="institutionError" @update:modelValue="institution = $event" />
    <DatePicker :modelValue="graduationYear" label="Рік закінчення" id="graduationYear" :error="graduationYearError" @update:modelValue="graduationYear = $event" />
    <CheckboxGroup :modelValue="certificates" label="Додаткові курси/сертифікати" :options="['Курс 1', 'Курс 2', 'Курс 3']" :error="certificatesError" @update:modelValue="certificates = $event" />

    <!-- Професійний досвід -->
    <WorkExperience v-model="experiences" />

    <!-- Мови -->
    <InputField :modelValue="nativeLanguage" label="Рідна мова" id="nativeLanguage" :error="nativeLanguageError" @update:modelValue="nativeLanguage = $event" />
    <CheckboxGroup :modelValue="languages" label="Володіння іншими мовами" :options="['Англійська', 'Німецька', 'Французька']" :error="languagesError" @update:modelValue="languages = $event" />
    <RadioGroup :modelValue="englishLevel" label="Рівень володіння англійською" :options="['Базовий', 'Середній', 'Високий']" id="englishLevel" :error="englishLevelError" @update:modelValue="englishLevel = $event" />

    <!-- Навички -->
    <InputField :modelValue="professionalSkills" label="Професійні навички" id="professionalSkills" :error="professionalSkillsError" @update:modelValue="professionalSkills = $event" />
    <RadioGroup :modelValue="leadership" label="Лідерські якості" :options="['1', '2', '3', '4', '5']" id="leadership" :error="leadershipError" @update:modelValue="leadership = $event" />
    <RadioGroup :modelValue="teamwork" label="Робота в команді" :options="['1', '2', '3', '4', '5']" id="teamwork" :error="teamworkError" @update:modelValue="teamwork = $event" />
    <CheckboxGroup :modelValue="softwareSkills" label="Володіння програмами" :options="['Microsoft Office', 'Photoshop', 'AutoCAD']" :error="softwareSkillsError" @update:modelValue="softwareSkills = $event" />
    <CheckboxGroup :modelValue="driversLicense" label="Водійські права" :options="['Так', 'Ні']" :error="driversLicenseError" @update:modelValue="driversLicense = $event" />

    <!-- Додаткові поля -->
    <CheckboxGroup :modelValue="businessTrips" label="Готовність до відряджень" :options="['Так', 'Ні']" :error="businessTripsError" @update:modelValue="businessTrips = $event" />
    <CheckboxGroup :modelValue="vehicle" label="Наявність власного транспорту" :options="['Так', 'Ні']" :error="vehicleError" @update:modelValue="vehicle = $event" />
    <InputField :modelValue="salary" label="Очікуваний рівень заробітної плати" id="salary" :error="salaryError" @update:modelValue="salary = $event" />
    <CheckboxGroup :modelValue="overtime" label="Можливість працювати понаднормово" :options="['Так', 'Ні']" :error="overtimeError" @update:modelValue="overtime = $event" />
    <InputField :modelValue="additionalSkills" label="Додаткові навички" id="additionalSkills" :error="additionalSkillsError" @update:modelValue="additionalSkills = $event" />

    <!-- Завантаження файлів -->
    <FileUpload label="Фото кандидата" id="photo" @update:files="updatePhoto" />
    <FileUpload label="Резюме" id="resume" @update:files="updateResume" />
    <FileUpload label="Мотиваційний лист" id="coverLetter" @update:files="updateCoverLetter" />
    <FileUpload label="Сертифікати" id="certificatesUpload" @update:files="updateCertificates" multiple />
    <FileUpload label="Портфоліо" id="portfolio" @update:files="updatePortfolio" />

    <!-- Проекти -->
    <ProjectField v-model="projects" />

    <!-- Соціальні мережі -->
    <InputField :modelValue="linkedinProfile" label="LinkedIn профіль" id="linkedinProfile" :error="linkedinProfileError" @update:modelValue="linkedinProfile = $event" />
    <InputField :modelValue="githubProfile" label="GitHub профіль" id="githubProfile" :error="githubProfileError" @update:modelValue="githubProfile = $event" />
    <InputField :modelValue="facebookProfile" label="Facebook профіль" id="facebookProfile" :error="facebookProfileError" @update:modelValue="facebookProfile = $event" />
    <InputField :modelValue="otherProfiles" label="Інші професійні профілі" id="otherProfiles" :error="otherProfilesError" @update:modelValue="otherProfiles = $event" />

    <!-- Захист від ботів -->
    <vue-recaptcha-v2 sitekey="your-site-key" @verify="onVerify" />

    <button type="submit" class="btn-primary">Надіслати</button>
  </form>
</template>


<style scoped>
@import "../assets/tailwind.css";
</style>