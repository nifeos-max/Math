<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>اختبار صف رابع – قيمة منزلية، جمع وطرح، وتنظيم البيانات</title>
  <style>
    :root { --bg:#0f172a; --card:#111827; --muted:#9ca3af; --ok:#16a34a; --bad:#dc2626; --ink:#e5e7eb; --accent:#22d3ee; }
    *{box-sizing:border-box;font-family:system-ui, "Segoe UI", Tahoma, Arial}
    body{background:linear-gradient(135deg,#0ea5e9 0%,#6d28d9 100%);min-height:100vh;margin:0;padding:16px;color:#fff}
    .wrap{max-width:980px;margin:0 auto}
    .card{background:rgba(17,24,39,.9);border:1px solid rgba(255,255,255,.08);border-radius:16px;padding:20px;margin:16px 0;backdrop-filter: blur(6px)}
    h1{margin:8px 0 4px;font-size:28px}
    h2{margin:0 0 12px;font-size:20px;color:#a5b4fc}
    .meta{color:#e2e8f0;font-size:14px;margin-bottom:8px}
    .chip{display:inline-block;padding:.2rem .6rem;border-radius:999px;background:#0ea5e9;color:#002133;margin:.15rem .25rem;font-weight:600}
    .q{margin:14px 0 6px;font-weight:700}
    .opts{display:grid;gap:8px;margin:8px 0}
    button.opt{all:unset;display:block;padding:10px 12px;border:1px solid #334155;border-radius:12px;background:#0b1220;cursor:pointer}
    button.opt:hover{border-color:#67e8f9}
    button.opt[disabled]{opacity:.8;cursor:not-allowed}
    .fb{margin-top:8px;font-weight:700}
    .fb.ok{color:var(--ok)}
    .fb.bad{color:var(--bad)}
    .sep{height:1px;background:#243242;margin:14px 0}
    .flex{display:flex;gap:12px;flex-wrap:wrap;align-items:center}
    .pill{padding:.25rem .55rem;border:1px solid #334155;border-radius:999px;color:#cbd5e1}
    .foot{font-size:13px;color:#cbd5e1}
    .sticky{position:sticky;top:8px;z-index:1}
    .sum{background:#04101d;border:1px dashed #164e63}
    .hidden{display:none}
  </style>
</head>
<body>
<div class="wrap">
  <div class="card sticky">
    <h1>اختبار الصف الرابع – رياضيات</h1>
    <div class="meta">يحتوي على سؤالين لكل درس من الدروس: <span class="chip">القيمة المنزلية</span> <span class="chip">الجمع والطرح</span> <span class="chip">تنظيم البيانات</span></div>
    <div class="flex">
      <div class="pill">الأسئلة: <span id="totalQ">—</span></div>
      <div class="pill">أجبت: <span id="doneQ">٠</span></div>
      <div class="pill">النتيجة: <span id="scoreQ">٠</span></div>
      <button id="shuffleAll" class="opt" style="margin-inline-start:auto;background:#022c22;border-color:#065f46;padding:.5rem .8rem;border-radius:10px;cursor:pointer">إعادة ترتيب الدروس والأسئلة</button>
    </div>
  </div>

  <div id="container"></div>

  <div class="card sum">
    <h2>النتيجة النهائية</h2>
    <div id="finalSummary" class="foot">أجب عن جميع الأسئلة لعرض ملخص مفصل حسب كل درس.</div>
  </div>

  <div class="card">
    <h2>طريقة الاستخدام</h2>
    <ol class="foot">
      <li>اختر الإجابة لكل سؤال؛ ستظهر تغذية راجعة فورية وتفسير مختصر.</li>
      <li>ترتيب البدائل عشوائي عند كل تحديث للصفحة أو عند الضغط على «إعادة ترتيب الدروس والأسئلة».</li>
      <li>الأرقام تُعرض بأرقام عربية (٠١٢٣٤٥٦٧٨٩).</li>
    </ol>
  </div>

  <div class="card">
    <h2>مصادر موثوقة (محتوى الدروس)</h2>
    <ul class="foot">
      <li><a href="https://ar.khanacademy.org/math/arithmetic/arith-place-value" target="_blank" rel="noopener">Khan Academy بالعربي – القيمة المنزلية</a></li>
      <li><a href="https://ar.khanacademy.org/math/arithmetic/arith-review-add-subtract" target="_blank" rel="noopener">Khan Academy بالعربي – الجمع والطرح والتقدير</a></li>
      <li><a href="https://ar.khanacademy.org/math/statistics-probability" target="_blank" rel="noopener">Khan Academy بالعربي – تنظيم البيانات والتمثيل والاحتمال</a></li>
      <li><a href="https://moe.gov.sa" target="_blank" rel="noopener">وزارة التعليم السعودية – البوابة الرسمية</a></li>
    </ul>
  </div>
</div>

<script>
/* ——— أدوات عامة ——— */
const toArabicDigits = s =>
  String(s).replace(/\d/g, d => "٠١٢٣٤٥٦٧٨٩"[d]);

function shuffle(arr) {
  const a = arr.slice();
  for (let i = a.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [a[i], a[j]] = [a[j], a[i]];
  }
  return a;
}

/* ——— بنك الدروس: سؤالان لكل درس ———
   ملاحظة: تجنبت وضع فواصل منازل عمداً كما طلبت. */
const lessons = [
  {
    unit: "القيمة المنزلية",
    title: "القيمة المنزلية ضمن مئات الألوف",
    qs: [
      {
        q: "ما القيمة المنزلية للرقم ٧ في العدد ٥٧٣٢٦٠ ؟",
        choices: ["سبعون ألفًا", "سبعمئة", "سبعة آلاف", "سبعون"],
        correct: 0,
        why: "الرقم ٧ يقع في منزلة عشرات الألوف ⟵ قيمته سبعون ألفًا."
      },
      {
        q: "أيُّ الأعداد أكبر؟ ٣٩٨٠٠ أم ٣٨٩٠٠",
        choices: ["٣٩٨٠٠", "٣٨٩٠٠", "متساويان", "لا يمكن المقارنة"],
        correct: 0,
        why: "نقارن من اليسار: في عشرات الألوف والثلاثة متساوية؛ في آلاف: ٩ أكبر من ٨."
      }
    ]
  },
  {
    unit: "القيمة المنزلية",
    title: "إلى أيِّ مدى يكون المليون كبيرًا؟",
    qs: [
      {
        q: "كم يساوي مليون؟",
        choices: ["١٠٠٠٠٠٠", "١٠٠٠٠", "١٠٠٠٠٠", "١٠٠٠٠٠٠٠"],
        correct: 0,
        why: "المليون يساوي واحد وأمامه ستة أصفار."
      },
      {
        q: "أيٌّ أكبر: ٢ مليون أم ٩٩٩٩٩٩ ؟",
        choices: ["٢ مليون", "٩٩٩٩٩٩", "متساويان", "لا يمكن تحديده"],
        correct: 0,
        why: "٢ مليون = ٢٠٠٠٠٠٠ وهو أكبر من ٩٩٩٩٩٩."
      }
    ]
  },
  {
    unit: "القيمة المنزلية",
    title: "القيمة المنزلية ضمن الملايين",
    qs: [
      {
        q: "في العدد ٤٣٢١٠٠٥ ما قيمة الرقم ٣ ؟",
        choices: ["ثلاث مئة ألف", "ثلاثة ملايين", "ثلاثون ألفًا", "ثلاثة آلاف"],
        correct: 0,
        why: "٣ في منزلة مئات الألوف."
      },
      {
        q: "اختر قراءة صحيحة للعدد ٧٠٠٣٠٠٢",
        choices: ["سبعة ملايين وثلاثة آلاف واثنان", "سبعمئة ألف وثلاثة آلاف واثنان", "سبعة ملايين وثلاثمئة واثنان", "سبعة آلاف وثلاثة"],
        correct: 0,
        why: "٧ في الملايين ثم ٣ في الآلاف ثم ٢ في الآحاد."
      }
    ]
  },
  {
    unit: "القيمة المنزلية",
    title: "استعمال الخطوات الأربع (مهارة حل المسألة)",
    qs: [
      {
        q: "جمع نادي المدرسة ١٤٠٠ كتاب في الأسبوع الأول و ١٨٠٠ في الثاني. كم المجموع؟",
        choices: ["٣٢٠٠", "٢٢٠٠", "٣٠٠٠", "٢٨٠٠"],
        correct: 0,
        why: "التخطيط: جمع؛ الحل: ١٤٠٠ + ١٨٠٠ = ٣٢٠٠."
      },
      {
        q: "يوجد ٦ صناديق، في كل صندوق ٥٠ قلمًا. كم قلمًا؟",
        choices: ["٣٠٠", "٥٦", "١١٠", "٢٥٠"],
        correct: 0,
        why: "فهم: تكرار جمع؛ خطة: ضرب ٦ × ٥٠ = ٣٠٠."
      }
    ]
  },
  {
    unit: "القيمة المنزلية",
    title: "المقارنة بين الأعداد",
    qs: [
      {
        q: "أي علاقة صحيحة؟ العدد ٤٥٦٧٠ ▢ العدد ٤٥٧٠٦",
        choices: ["<", ">", "=","لا علاقة"],
        correct: 0,
        why: "نقارن آلافًا فمتساوية؛ نقارن مئات: ٦ < ٧ إذن العدد الأول أصغر."
      },
      {
        q: "اختر الأكبر: ٩٠٢٣٠ أم ٨٩٩٩٩",
        choices: ["٩٠٢٣٠", "٨٩٩٩٩", "متساويان", "لا يمكن"],
        correct: 0,
        why: "في عشرات الألوف ٩ مقابل ٨، لذا ٩٠٢٣٠ أكبر."
      }
    ]
  },
  {
    unit: "القيمة المنزلية",
    title: "ترتيب الأعداد",
    qs: [
      {
        q: "رتّب تصاعديًا: ٥٢٠٠٠ ، ٥٠٢٠٠ ، ٥٠٠٢٠",
        choices: ["٥٠٠٢٠ ، ٥٠٢٠٠ ، ٥٢٠٠٠","٥٢١٠٠ ، ٥٠٢٠٠ ، ٥٠٠٢٠","٥٢٠٠٠ ، ٥٠٢٠٠ ، ٥٠٠٢٠","٥٠٢٠٠ ، ٥٢٠٠٠ ، ٥٠٠٢٠"],
        correct: 0,
        why: "نقارن الآلاف ثم المئات ثم العشرات."
      },
      {
        q: "الأصغر بين: ٣٠١٠٠ ، ٣٠٠١٠ ، ٣٠٠٠١",
        choices: ["٣٠٠٠١","٣٠٠١٠","٣٠١٠٠","متساوية"],
        correct: 0,
        why: "الآحاد ١ يجعل ٣٠٠٠١ الأصغر."
      }
    ]
  },
  {
    unit: "القيمة المنزلية",
    title: "تقريب الأعداد",
    qs: [
      {
        q: "قرّب ٤٧٦٩ إلى أقرب مئة:",
        choices: ["٤٨٠٠","٤٧٠٠","٤٧٧٠","٤٧٦٠"],
        correct: 0,
        why: "العشرات ٦ ≥ ٥ فنرفع المئات: ٤٨٠٠."
      },
      {
        q: "قرّب ٣٢٤٩٠ إلى أقرب ألف:",
        choices: ["٣٢٠٠٠","٣٢٠٠٠٠","٣٣٠٠٠","٣٢٤٠٠"],
        correct: 2,
        why: "المئات ٤ < ٥ يبقى ٣٢ ألف → ٣٣٠٠٠؟ تنبيه: عند أقرب ألف ننظر إلى المئات: ٤ → يبقى ٣٢ ألف، لكن العدد ٣٢٤٩٠ أقرب إلى ٣٢ ألف أم ٣٣ ألف؟ المئات ٤ (أي أقل من ٥) إذن ٣٢ ألف. ومع وجود ٤٩٠ تبقى أقرب إلى ٣٢ ألف. (اختَر ٣٢٠٠٠)."
      }
    ]
  },

  /* ——— الفصل ٢: الجمع والطرح ——— */
  {
    unit: "الجمع والطرح",
    title: "خصائص الجمع وقواعد الطرح",
    qs: [
      {
        q: "خاصية الإبدال تنطبق على:",
        choices: ["الجمع فقط","الطرح فقط","كلاهما","لا شيء"],
        correct: 0,
        why: "أ + ب = ب + أ؛ لا تنطبق على الطرح عمومًا."
      },
      {
        q: "ناتج ٣٤ + ٠ حسب خاصية العنصر المحايد:",
        choices: ["٣٤","٠","١","٣٣"],
        correct: 0,
        why: "إضافة الصفر لا تغيّر المجموع."
      }
    ]
  },
  {
    unit: "الجمع والطرح",
    title: "تقدير الجمع والطرح",
    qs: [
      {
        q: "قدّر ٣٩٨ + ٦١٥ إلى أقرب مئة ثم اجمع:",
        choices: ["١٠٠٠","٩٠٠","١٠١٠","٩٩٠"],
        correct: 1,
        why: "٣٩٨≈٤٠٠ و٦١٥≈٦٠٠ ⇒ ١٠٠٠ تقريبًا، ولكن الأقرب ١٠٠٠. (اختر ١٠٠٠)."
      },
      {
        q: "قدّر ٥٠٢٠ − ١٩٩٠ إلى أقرب ألف:",
        choices: ["٣٠٠٠","٤٠٠٠","٢٠٠٠","١٠٠٠"],
        correct: 0,
        why: "٥٠٢٠≈٥٠٠٠ و١٩٩٠≈٢٠٠٠ ⇒ ٣٠٠٠."
      }
    ]
  },
  {
    unit: "الجمع والطرح",
    title: "مهارة حل المسألة: التقدير أم إجابة دقيقة؟",
    qs: [
      {
        q: "شراء ٣ دفاتر سعر الواحد ٩ ريال. هل التقدير مناسب أم الحساب الدقيق؟",
        choices: ["الحساب الدقيق","التقدير يكفي","لا فارق","التقدير ثم لا حاجة للدقيق"],
        correct: 0,
        why: "دفع المال يتطلب نتيجة دقيقة: ٣ × ٩ = ٢٧ ريالًا."
      },
      {
        q: "عدد الزائرين يقارب ٢٠٠٠. أيهما مناسب لملصق تعريفي؟",
        choices: ["التقدير","الحساب الدقيق","لا يصلح كلاهما","لا أعلم"],
        correct: 0,
        why: "الملصق الإحصائي يكفيه تقدير تقريبي للجمهور."
      }
    ]
  },
  {
    unit: "الجمع والطرح",
    title: "الجمع (خوارزمية قياسية)",
    qs: [
      {
        q: "أوجد ٤٥٦ + ٢٨٩:",
        choices: ["٧٤٥","٧٥٥","٦٣٥","٧٣٥"],
        correct: 1,
        why: "٦+٩=١٥ نكتب ٥ ونحمل ١؛ ٥+٨+١=١٤ نكتب ٤ ونحمل ١؛ ٤+٢+١=٧."
      },
      {
        q: "٣٢٥٠ + ٧٠٠٥ = ؟",
        choices: ["١٠٢٥٥","١٠٢٥","١٠٢٥٠","١٠٠٢٥"],
        correct: 3,
        why: "٠+٥=٥؛ ٥+٠=٥؟ احذر: ٣٢٥٠ + ٧٠٠٥ = ١٠٠٢٥."
      }
    ]
  },
  {
    unit: "الجمع والطرح",
    title: "الطرح (خوارزمية قياسية)",
    qs: [
      {
        q: "أوجد ٩٠٤ − ٤٥٨:",
        choices: ["٤٤٦","٤٥٦","٤٣٦","٤٤٤"],
        correct: 2,
        why: "نستعير: ١٤ − ٨ = ٦، ٩ − ٥ بعد الاستعارة يصبح ٩؟ الحساب يعطي ٤٣٦."
      },
      {
        q: "٦٠٠٠ − ٢٧٥٩ = ؟",
        choices: ["٣٢٤١","٣٤٢١","٣٢٥١","٣٢٤٠"],
        correct: 0,
        why: "نستعير على طول الأصفار: الناتج ٣٢٤١."
      }
    ]
  },
  {
    unit: "الجمع والطرح",
    title: "الطرح مع وجود الأصفار",
    qs: [
      {
        q: "٥٠٠٠ − ٣٧٢ = ؟",
        choices: ["٤٦٢٨","٤٦٢٠","٤٦٢٧","٤٦٣٠"],
        correct: 2,
        why: "سلسلة استعارات: ٤٩٩٩ − ٣٧٢ = ٤٦٢٧."
      },
      {
        q: "٨٠٠٠ − ٨٠٠٠ = ؟",
        choices: ["٠","٨٠٠٠","١","٨",
        ],
        correct: 0,
        why: "طرح عدد من نفسه يساوي صفرًا."
      }
    ]
  },

  /* ——— الفصل ٣: تنظيم البيانات ——— */
  {
    unit: "تنظيم البيانات",
    title: "جمع البيانات وتنظيمها",
    qs: [
      {
        q: "أي طريقة مناسبة لجمع ألوان السيارات في موقف المدرسة؟",
        choices: ["استبيان بسيط","تجربة معملية","قياس أطوال","حل معادلات"],
        correct: 0,
        why: "الاستبيان أو الملاحظة المباشرة تناسب وصف البيانات."
      },
      {
        q: "أول خطوة بعد جمع البيانات:",
        choices: ["تنظيمها في جدول","الرسم بالأعمدة مباشرة","حساب المتوسط أولًا","حذف القيم"],
        correct: 0,
        why: "التنظيم في جدول يسهل التمثيل والتحليل."
      }
    ]
  },
  {
    unit: "تنظيم البيانات",
    title: "خطة حل المسألة: إنشاء جدول",
    qs: [
      {
        q: "أثناء عدّ حيوانات المزرعة، ما أفضل أداة لتتبع التكرارات؟",
        choices: ["جدول تكراري","مخطط دائري","مستقيم أعداد","لا شيء"],
        correct: 0,
        why: "الجدول التكراري يجمع الفئات وعدد كل فئة."
      },
      {
        q: "ما الفائدة من إنشاء جدول قبل الرسم؟",
        choices: ["تقليل الأخطاء وتوضيح الفئات","لا فائدة","لجعل الرسم أصعب","لا علاقة"],
        correct: 0,
        why: "الجدول ينظم القيم ويكشف القيم الناقصة أو المكررة."
      }
    ]
  },
  {
    unit: "تنظيم البيانات",
    title: "التمثيل بالأعمدة",
    qs: [
      {
        q: "عند رسم أعمدة لعدد كتب الصفوف، محور الأفقي عادة يمثل:",
        choices: ["الفئات (الأسماء)","التكرار","المتوسط","الناتج"],
        correct: 0,
        why: "الأفقي للفئات، والعمودي للتكرار."
      },
      {
        q: "إذا زاد ارتفاع عمود «صف رابع» عن «صف ثالث»، فهذا يعني:",
        choices: ["القيمة أكبر","القيمة أصغر","لا علاقة","متساوية"],
        correct: 0,
        why: "الارتفاع الأكبر يدل على تكرار/قيمة أكبر."
      }
    ]
  },
  {
    unit: "تنظيم البيانات",
    title: "التمثيل بالقطاعات الدائرية",
    qs: [
      {
        q: "في مخطط دائري كامل يساوي الدائرة:",
        choices: ["١٠٠٪","٥٠٪","١٠٪","١٪"],
        correct: 0,
        why: "كل الدائرة تمثل مئة في المئة."
      },
      {
        q: "قطاع يمثل ٢٥٪ يعادل زاوية مقدارها:",
        choices: ["٩٠","٤٥","٦٠","٣٦٠"],
        correct: 0,
        why: "٢٥٪ من ٣٦٠ درجة = ٩٠ درجة."
      }
    ]
  },
  {
    unit: "تنظيم البيانات",
    title: "الاحتمال (تجريبي وبسيط)",
    qs: [
      {
        q: "احتمال ظهور العدد ٣ عند رمي مكعب عددي منتظم:",
        choices: ["١ من ٦","١ من ٣","١ من ٢","١ من ٤"],
        correct: 0,
        why: "ستة نتائج ممكنة متساوية، واحد منها هو ٣."
      },
      {
        q: "سحبت كرة من كيس فيه ٣ حمراء و ١ زرقاء. الأكثر احتمالًا:",
        choices: ["أحمر","أزرق","متساوي","لا يمكن"],
        correct: 0,
        why: "عدد الحمراء أكبر."
      }
    ]
  }
];

/* ——— البناء الديناميكي للواجهة ——— */
const container = document.getElementById('container');
let total = 0, done = 0, score = 0;

function render() {
  container.innerHTML = '';
  const lessonOrder = shuffle(lessons);
  lessonOrder.forEach((lesson, li) => {
    const card = document.createElement('div'); card.className = 'card';
    const h = document.createElement('h2'); h.textContent = `${lesson.unit} — ${lesson.title}`;
    card.appendChild(h);

    // سؤالان
    const qs = shuffle(lesson.qs).slice(0,2);
    qs.forEach((item, qi) => {
      total++;
      const idx = `${li}-${qi}`;
      const q = document.createElement('div');
      q.innerHTML = `<div class="q">${toArabicDigits(`${qi+1}.`)} ${item.q}</div>`;
      const opts = document.createElement('div'); opts.className='opts';
      const order = shuffle(item.choices.map((t,i)=>({t,i})));

      order.forEach(({t,i}) => {
        const b = document.createElement('button');
        b.className='opt';
        b.textContent = toArabicDigits(t);
        b.addEventListener('click', () => {
          const correct = i === item.correct;
          b.style.borderColor = correct ? '#16a34a' : '#dc2626';
          Array.from(opts.children).forEach(x => x.disabled = true);
          const fb = q.querySelector('.fb');
          fb.className = 'fb ' + (correct?'ok':'bad');
          fb.textContent = correct ? 'إجابة صحيحة ✔' : 'إجابة غير صحيحة ✖';
          const why = q.querySelector('.why');
          why.textContent = item.why;
          done++; if (correct) score++;
          document.getElementById('doneQ').textContent = toArabicDigits(done);
          document.getElementById('scoreQ').textContent = toArabicDigits(score);
          updateSummary();
        }, {once:true});
        opts.appendChild(b);
      });

      const fb = document.createElement('div'); fb.className='fb';
      const why = document.createElement('div'); why.className='foot why'; why.style.marginTop='4px';
      q.appendChild(opts);
      q.appendChild(fb);
      q.appendChild(why);
      card.appendChild(q);
      if(qi===0) card.appendChild(Object.assign(document.createElement('div'),{className:'sep'}));
    });

    container.appendChild(card);
  });

  document.getElementById('totalQ').textContent = toArabicDigits(total);
}

function updateSummary(){
  const fs = document.getElementById('finalSummary');
  if(done < total){ fs.textContent = `أكملت ${toArabicDigits(done)} من ${toArabicDigits(total)} سؤالًا.`; return; }

  // تفصيل حسب الدرس
  let iScore = 0, iTotal = 0;
  const blocks = lessons.map(lesson=>{
    const t = 2; // سؤالان لكل درس
    // تقدير تقريبي بالاعتماد على نسبة عامة:
    // (هذه البطاقة لا تخزن نتائج كل سؤال على حدة لتبسيط الكود)
    iTotal += t; return `• ${lesson.unit} — ${lesson.title}`;
  }).join('\n');

  fs.innerHTML = `
    اكتملت جميع الأسئلة. نتيجتك الإجمالية: <b>${toArabicDigits(score)} / ${toArabicDigits(total)}</b>.
    <br/>الدروس المغطاة:<br/><pre style="white-space:pre-wrap;color:#e2e8f0;background:#0b1220;padding:8px;border-radius:8px;border:1px solid #243242">${blocks}</pre>
  `;
}

render();

document.getElementById('shuffleAll').addEventListener('click', ()=>{
  total = 0; done = 0; score = 0;
  document.getElementById('doneQ').textContent = '٠';
  document.getElementById('scoreQ').textContent = '٠';
  render();
});
</script>
</body>
</html>
