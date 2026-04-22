تكليف مكدس التنقل | Basic Stack Navigation

• الاسم: رهف عادل عبدالوهاب

• التخصص: تقنية معلومات

شرح التكليف

المشروع يوضح كيفية إدارة التنقل بين الشاشات في تطبيقات فلاتر باستخدام نظام المكدس (Stack)، وكيفية تمرير البيانات من شاشة إلى أخرى واستلام نتيجة عند العودة.

التقرير التقني (آلية عمل المكدس)

في هذا التكليف، ركزت على تطبيق المفاهيم الأساسية لنظام التنقل (Navigator) لضمان انتقال سلس بين واجهات التطبيق:


• إدارة المكدس (Navigator Stack): استخدمت نظام المكدس حيث يتم "دفع" الشاشة الجديدة (push) فوق الشاشة الحالية، و "سحبها" (pop) عند العودة، وهذا يتبع مبدأ LIFO (الداخل أخيراً يخرج أولاً).

• تمرير واستلام البيانات: قمت ببرمجة التطبيق ليمرر "اسم المنتج" من الشاشة الرئيسية إلى شاشة التفاصيل عبر الـ Constructor، وعند العودة، ترسل الشاشة الثانية نتيجة نصية تؤكد نجاح العملية.

• واجهة المستخدم (UI): استخدمت SnackBar لعرض الرسالة القادمة من الشاشة الثانية، مما يحسن من تفاعل المستخدم مع التطبيق.


• برمجة آمنة: حرصت على إضافة فحص context.mounted بعد عمليات الـ await للتأكد من استقرار التطبيق ومنع الأخطاء البرمجية أثناء التنقل.


لقطات شاشة المخرجات (Screen Shoots)



<img width="205" height="425" alt="‏‏لقطة الشاشة (155)" src="https://github.com/user-attachments/assets/5e133255-5c8b-4e08-9b95-7278254bc9a9" />



<img width="209" height="412" alt="‏‏لقطة الشاشة (156)" src="https://github.com/user-attachments/assets/158bc5fa-5bc2-43ea-b6bd-06206a49943e" />



<img width="209" height="428" alt="‏‏لقطة الشاشة (157)" src="https://github.com/user-attachments/assets/a88def9c-4f97-4f79-8879-c7e92f5027ad" />



الكود الكامل | Full Code



import 'package:flutter/material.dart';

void main() {
  runApp(const FinalNavigationApp());
}

class FinalNavigationApp extends StatelessWidget {
  const FinalNavigationApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Navigation Exercise',
      debugShowCheckedModeBanner: false,
      builder: (context, child) {
        return Directionality(
          textDirection: TextDirection.rtl,
          child: child!,
        );
      },
      theme: ThemeData(
        useMaterial3: true,
        scaffoldBackgroundColor: const Color(0xFFF2F2F7),
        appBarTheme: const AppBarTheme(
          backgroundColor: Colors.transparent,
          elevation: 0,
          centerTitle: true,
          titleTextStyle: TextStyle(
              color: Colors.black87,
              fontSize: 20,
              fontWeight: FontWeight.bold
          ),
          iconTheme: IconThemeData(color: Colors.black87),
        ),
      ),
      home: const ProductListScreen(),
    );
  }
}

// الشاشة الأولى: Product List Screen
class ProductListScreen extends StatelessWidget {
  const ProductListScreen({super.key});

  Future<void> _navigateToDetails(BuildContext context, String productName) async {
    final result = await Navigator.push(
      context,
      MaterialPageRoute(
        builder: (context) => ProductDetailsScreen(productName: productName),
      ),
    );

    if (!context.mounted) return;

    if (result != null) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(
          content: Row(
            children: [
              const Icon(Icons.check_circle, color: Colors.white),
              const SizedBox(width: 10),
              Text(result.toString(), style: const TextStyle(fontSize: 16)),
            ],
          ),
          behavior: SnackBarBehavior.floating,
          backgroundColor: Colors.green.shade700,
          shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(10)),
          margin: const EdgeInsets.all(16),
        ),
      );
    }
  }

  @override
  Widget build(BuildContext context) {
    final List<String> products = [
      'هاتف ذكي (Smartphone)',
      'حاسوب محمول (Laptop Pro)',
      'سماعات لاسلكية (Earbuds)'
    ];

    return Scaffold(
      appBar: AppBar(title: const Text('قائمة المنتجات')),
      body: ListView.builder(
        padding: const EdgeInsets.all(16),
        itemCount: products.length,
        itemBuilder: (context, index) {
          return Container(
            margin: const EdgeInsets.only(bottom: 12),
            decoration: BoxDecoration(
              color: const Color(0xFFFFFFFF),
              borderRadius: BorderRadius.circular(16),
              boxShadow: [
                BoxShadow(
                    color: Colors.black.withOpacity(0.04),
                    blurRadius: 8,
                    offset: const Offset(0, 3)
                ),
              ],
            ),
            child: ListTile(
              contentPadding: const EdgeInsets.symmetric(horizontal: 20, vertical: 8),
              leading: const Icon(Icons.shopping_bag_outlined, color: Colors.blueAccent),
              title: Text(products[index], style: const TextStyle(fontWeight: FontWeight.bold)),
              trailing: const Icon(Icons.arrow_forward_ios, size: 16),
              onTap: () => _navigateToDetails(context, products[index]),
            ),
          );
        },
      ),
    );
  }
}

// الشاشة الثانية: Product Details Screen
class ProductDetailsScreen extends StatelessWidget {
  final String productName;

  const ProductDetailsScreen({super.key, required this.productName});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('تفاصيل المنتج')),
      body: Center(
        child: Padding(
          padding: const EdgeInsets.all(24.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              Text(
                'لقد قمت باختيار:',
                style: TextStyle(fontSize: 18, color: Colors.grey.shade600),
              ),
              const SizedBox(height: 10),
              Text(
                productName,
                textAlign: TextAlign.center,
                style: const TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
              ),
              const SizedBox(height: 40),

              SizedBox(
                width: double.infinity,
                height: 55,
                child: ElevatedButton.icon(
                  onPressed: () {
                    Navigator.pop(context, 'تم الاضافة للمفضلة');
                  },
                  icon: const Icon(Icons.favorite, color: Colors.white),
                  label: const Text(
                    'إضافة للمفضلة',
                    style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold, color: Colors.white),
                  ),
                  style: ElevatedButton.styleFrom(
                    backgroundColor: Colors.blueAccent,
                    elevation: 0,
                    shape: RoundedRectangleBorder(
                      borderRadius: BorderRadius.circular(16),
                    ),
                  ),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}






توضيح حالات المكدس (Stack States Visualization)





<img width="1280" height="698" alt="6001454835061951877_121" src="https://github.com/user-attachments/assets/93450eab-6472-4d1a-860b-c39333d04458" />
