# NYTimes Objective-C スタイルガイド

このスタイルガイドは The New York Time 内の iOS チームにおけるコーディング上の慣習の概略を述べたものです。 [issues](https://github.com/NYTimes/objetive-c-style-guide/issues) や [pull requests](https://github.com/NYTimes/objetive-c-style-guide/pulls)、 [tweets](https://twitter.com/nytimesmobile) からのフィードバックを歓迎します。また、 [私たちは現在求人中です](http://jobs.nytco.com/job/New-York-iOS-Developer-Job-NY/2572221/) 。

[このスタイルガイドに対する貢献者](https://github.com/NYTimes/objective-c-style-guide/contributors) に御礼申し上げます。

## はじめに

スタイルガイドに関する事項を含む Apple によるドキュメントが存在します。本書で言及されていないことであっても、以下のどれかにて詳細に網羅されているかもしれません。

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## 目次

* [ドット表記構文](#ドット表記構文)
* [スペーシング](#スペーシング)
* [条件分岐](#条件分岐)
  * [三項演算子](#三項演算子)
* [エラーハンドリング](#エラーハンドリング)
* [メソッド](#メソッド)
* [変数](#変数)
* [命名](#命名)
* [コメント](#コメント)
* [init と dealloc](#init-と-dealloc)
* [リテラル](#リテラル)
* [CGRect 関数](#CGRect-関数)
* [定数](#定数)
* [列挙型](#列挙型)
* [ビットマスク](#ビットマスク)
* [プライベートプロパティ](#プライベートプロパティ)
* [画像の命名](#画像の命名)
* [真偽値](#真偽値)
* [シングルトン](#シングルトン)
* [インポート](#インポート)
* [Xcode プロジェクト](#xcode-プロジェクト)

## ドット表記構文

ドット表記は **必ず** プロパティに対するアクセスもしくは変更のために使うべきです。そのほかの場合は大括弧表記が適切です。

**良い例:**
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**悪い例:**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## スペーシング

* スペース 4 つでインデントしてください。タブでインデントしてはいけません。 Xcode 上でこの設定をおこなっているか確認してください。
* メソッドを含むすべて (`if`/`else`/`switch`/`while` など) の中括弧は必ず文と同じ行からはじめ、新しい行で閉じるようにしてください。

**良い例:**
```objc
if (user.isHappy) {
//Do something
}
else {
//Do something else
}
```
* 視認性や組織化を促進するためにメソッド間は一行開けておくべきです。メソッド内の空白文字は機能別で区切られるべきですが、たいていの場合は新しくメソッド化するべきかもしれません。
* `@synthesize` と `@dynamic` は実装部に一行ずつ書くべきです。

## 条件分岐

条件節は中括弧なしで記述できる場合 (たとえば一行のみの場合) であっても、 [間違い](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256) 防止のために必ず中括弧を記述するべきです。 if 文の一部となるべき第二の行を追加する場合の間違いも含まれますが、むしろ [いっそう危険な欠点](http://programmers.stackexchange.com/a/16530) として、 if 文中の行をコメントアウトした際に意図せずに次の行が if 文の一部となってしまうという問題が存在します。加えて、このスタイルはほかのすべての条件分岐と一貫性があり、ゆえに検査が容易です。

**良い例:**
```objc
if (!error) {
    return success;
}
```

**悪い例:**
```objc
if (!error)
    return success;
```

または、

```objc
if (!error) return success;
```

### 三項演算子

三項演算子 ? はコードがきちんとしていて、わかりやすくなる場合にのみ使用されるべきです。単一の条件は基本的にはすべて問題ないとみるべきでしょう。複数の条件は通常 if 文とするか、あるいはインスタンス変数にリファクタリングするほうがよりわかりやすくなると考えられます。

**良い例:**
```objc
result = a > b ? x : y;
```

**悪い例:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## エラーハンドリング

メソッドがエラー用パラメータを参照で返す場合、エラー用変数ではなく返り値で判定するようにしてください。

**良い例:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**悪い例:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

いくつかの Apple の API は成功時にエラー用パラメータにゴミ (NULL でない) を書き込んでしまうため、エラー時の分岐で誤った判定 (false negative) をおこなってしまいますし、クラッシュに繋がるかもしれません。

## メソッド

メソッドのシグネチャでは、スコープ (-/+ シンボル) の後にスペースを開けるようにしてください。メソッドのセグメントの間にもスペースを置いてください。

**例**:
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```
## 変数

変数は可能な限り説明的に命名してください。一文字の変数名は `for()` ループを除いて避けなければなりません。

ポインタを示すアスタリスクは、たとえば `NSString* text` や `NSString * text` ではなく `NSString *text` といったように変数に付随するようにするべきですが、定数の場合は例外です。

極力、生のインスタンス変数ではなくプロパティ定義を使用するべきです。直接インスタンス変数にアクセスすることは初期化メソッド (`init` や `initWithCoder:` など) や `dealloc` メソッド、独自のセッターやゲッター内を除いて避けるべきです。初期化メソッドや dealloc 内でのアクセサメソッドの使い方についての詳しい情報は [こちら](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6) を参照してください。

**良い例:**

```objc
@interface NYTSection: NSObject

@property (nonatomic) NSString *headline;

@end
```

**悪い例:**

```objc
@interface NYTSection : NSObject {
    NSString *headline;
}
```

#### 変数修飾子

[ARC と一緒に導入された](https://developer.apple.com/library/ios/releasenotes/objectivec/rn-transitioningtoarc/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4) 変数修飾子に関しては、修飾子 (`__strong`, `__weak`, `__unsafe_unretained`, `__autoreleasing`) は `NSString * __weak text` のようにしてアスタリスクと変数名の間に配置されるべきです。

## 命名

特に [メモリ管理ルール](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)) に関する事項については、Apple の命名規則を可能な限り尊重するべきです。

長く説明的なメソッド名や変数名が好ましいです。

**良い例:**

```objc
UIButton *settingsButton;
```

**悪い例**

```objc
UIButton *setBut;
```

3 文字の接尾辞 (例: `NYT`) はクラス名や定数名において使われるべきですが、 Core Data のエンティティ名において省略するのは構いません。わかりやすさのために、定数はすべての単語の先頭を大文字にしてクラス名に関連した接尾辞を含むキャメルケースとすべきです。

**良い例:**

```objc
static const NSTimeInterval NYTArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**悪い例:**

```objc
static const NSTimeInterval fadetime = 1.7;
```

プロパティやローカル変数は先頭の単語を小文字ではじめるキャメルケースとするべきです。

インスタンス変数は先頭の単語を小文字ではじめるキャメルケースとするべきで、かつ、アンダースコアを前置するようにしてください。これで LLVM によって自動的に合成されたインスタンス変数との一貫性がとれます。 **もし LLVM が変数を自動的に合成できる場合にはそれを許します。**

**良い例:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**悪い例:**

```objc
id varnm;
```

## コメント

コメントは特定のコード部品が「なぜ」なにがしかの処理をするのかを説明するのに必要なときに使われるべきです。すべてのコメントは更新ないし削除をし続けるようにしてください。

できるだけコード自体がドキュメントとなるように心がけるようにし、散発的でいくつかの行に渡った説明を加える目的を除き、ブロックコメントは基本的には避けられるべきです。これはドキュメント生成用のコメント群には適用されません。

## init と dealloc

`dealloc` メソッドは実装部の先頭の、 `@synthesize` や `@dynamic` の直下に配置されるべきです。 `init` はどんなクラスにおいても `dealloc` の直下に配置されるべきです。

`init` メソッドは以下のようにして構成されるべきです:

```objc
- (instancetype)init {
    self = [super init]; // or call the designated initializer
    if (self) {
        // Custom initialization
    }

    return self;
}
```

## リテラル

`NSString`, `NSDictionary`, `NSArray`, や `NSNumber` リテラルはこれらのオブジェクトのイミュータブルなインスタンスを生成するときは必ず使うべきです。 `nil` 値を `NSArray` や `NSDictionary` リテラルに渡してしまうとクラッシュを引き起こすため、特別な配慮をしてください。

**良い例:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**悪い例:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## CGRect 関数

`CGRect` の `x` や `y`, `width`, `height` にアクセスする場合には、直接構造体のメンバにアクセスするのではなく [`CGGeometry` 関数](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) を使用するようにしてください。 Apple の `CGGeometry` リファレンスによれば、

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**良い例:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**悪い例:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## 定数

汎用的に使われる変数を容易に再利用したり、検索や置換の必要なくすぐに変更ができるために、定数はインラインの文字列や数字よりも優れています。定数は `static` 定数として定義されるべきで、 `#define` はマクロとして利用する場合を除いて使用するべきではありません。

**良い例:**

```objc
static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat NYTImageThumbnailHeight = 50.0;
```

**悪い例:**

```objc
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```

## 列挙型

固定された潜在型 (fixed underlying type) 仕様を新しく使用する際、強い型チェックとコード補完を持つ `enum` を利用することが推奨されます。 SDK は `NS_ENUM()` という、固定された潜在型 (fixed underlying type) の利用を促進する心強いマクロを含んでいます。

**例:**

```objc
typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    NYTAdRequestStateInactive,
    NYTAdRequestStateLoading
};
```

## ビットマスク

ビットマスクを利用する際は `NS_OPTIONS` マクロを活用してください。

**例:**

```objc
typedef NS_OPTIONS(NSUInteger, NYTAdCategory) {
  NYTAdCategoryAutos      = 1 << 0,
  NYTAdCategoryJobs       = 1 << 1,
  NYTAdCategoryRealState  = 1 << 2,
  NYTAdCategoryTechnology = 1 << 3
};
```

## プライベートプロパティ

プライベートプロパティはクラス拡張 (無名カテゴリ) としてクラスの実装ファイルにて定義されるべきです。名前付きカテゴリ (`NYTPrivate` や `private` のようなカテゴリ) 他のクラスを拡張する目的以外では使わないでください。

**例:**

```objc
@interface NYTAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## 画像の命名

画像の名前はまとまりや開発者の心の平静を維持するために一貫して命名されるべきです。 (存在する場合) 拡張されるクラスやプロパティの接尾辞なしの名前や、色や位置、その状態に至るまで、画像の目的の説明を含むキャメルケースの文字列として命名してください。

**例:**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` や `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` や `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

似た目的で使用される画像は Images フォルダーのそれぞれのグループにてグルーピングされるべきです。

## 真偽値

`nil` は `NO` として解決されるため、条件式で比較する必要はありません。`YES` は `BOOL` の 1 であり 8 ビットで表されるので、 `YES` と何かを直接比較することはしないでください。

これはファイル間の一貫性を高め、視覚的なわかりやすさを向上させます。

**良い例:**

```objc
if (!someObject) {
}
```

**悪い例:**

```objc
if (someObject == nil) {
}
```

-----

**`BOOL` に関する良い例:**

```objc
if (isAwesome)
if (![someObject boolValue])
```

**悪い例:**

```objc
if (isAwesome == YES) // Never do this.
if ([someObject boolValue] == NO)
```

-----

`BOOL` 型のプロパティの名前が形容詞として表現される場合は "is" プレフィックスを省略できますが、 get アクセサに対する名前を慣例として指定してください。たとえば、

```objc
@property (assign, getter=isEditable) BOOL editable;
```
テキストと例は [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE) からのものです.

## シングルトン

シングルトンオブジェクトは共有インスタンスのためのスレッドセーフパターンを使うべきです。
```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```
これは [頻発しうるクラッシュ増加の可能性](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html) を防ぐことになります。

## インポート

ひとつ以上の import 文が存在する場合は、文を [グルーピング](http://ashfurrow.com/blog/structuring-modern-objective-c) するようにしてください。各グループに対するコメントは任意です。

注意: モジュールに対しては [@import](http://clang.llvm.org/docs/Modules.html#using-modules) 構文を使うようにしてください。

```objc
// Frameworks
@import QuartzCore;

// Models
#import "NYTUser.h"

// Views
#import "NYTButton.h"
#import "NYTUserView.h"
```

## Xcode プロジェクト

ファイルが散らかるのを回避するために、物理ファイルは Xcode プロジェクトファイルと同期している状態を保つようにしてください。作成したすべての Xcode グループがファイルシステム上のフォルダを反映するようにしてください。わかりやすさのために、コードは型だけではなく機能毎にグルーピングするようにしてください。

可能であれば、 target の Build Setting で "Treat Warnings as Errors" をオンにし、できるだけ多くの [追加の warning](http://boredzo.org/blog/archives/2009-11-07/warnings) を有効にしてください。特定の warning を無視する場合は [Clang の pragma 機能](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas) を利用してください。

# 他の Objective-C スタイルガイド

我々のものが肌に合わない場合は他のスタイルガイドも参考にしてください:

* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
