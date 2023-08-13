# 애노테이션 프로세서
## Lombok(롬복)은 어떻게 동작하는 걸까?
Lombok은 @Getter, @Setter, @Builder 등의 애노테이션과 애노테이션 프로세서를 제공하여 표준적으로 작성해야 할 코드를 개발자 대신 생성해주는 라이브러리.  
  
롬복 동작 원리  
- 컴파일 시점에 애노테이션 프로세서를 사용하여 소스코드의 AST(abstract syntax tree)를 조작한다.  
  
애노테이션 프로세서는 **컴파일할 때** 특정한 애노테이션이 붙어 있는 소스코드를 참조해서 또 다른 소스코드를 만들어낼 수 있는 기능.  
  
공개된 API(TypeElement, RoundEnvironment)는 참조만 가능한데, 하위 타입으로 캐스팅한 후 처리한다.  
  
논란 거리  
- 공개된 API가 아닌 컴파일러 내부 클래스를 사용하여 기존 소스 코드를 조작한다.  
- 특히 이클립스의 경우엔 java agent를 사용하여 컴파일러 클래스까지 조작하여 사용한다. 해당 클래스들 역시 공개된 API가 아니다보니 버전 호환성에 문제가 생길 수 있고 언제라도 그런 문제가 발생해도 이상하지 않다.  
- 그럼에도 불구하고 엄청난 편리함 때문에 널리 쓰이고 있으며 대안이 몇가지 있지만 롬복의 모든 기능과 편의성을 대체하진 못하는 현실이다.  
  
## 애노테이션 프로세서 1부
[Processor 인터페이스](https://docs.oracle.com/en/java/javase/11/docs/api/java.compiler/javax/annotation/processing/Processor.html)  
- 여러 라운드에 거쳐 소스 및 컴파일 된 코드를 처리할 수 있다.  
  
[AutoService](https://github.com/google/auto/tree/main/service)  
- 서비스 프로바이더 레지스트리 생성기  
- 컴파일 시점에 애노테이션 프로세서를 사용해서 META-INF/services/javax.annotation.processor.Proccesor 파일을 자동으로 생성해준다.  
  
## 애노테이션 프로세서 2부
```java
@AutoService(Processor.class)
public class MagicMojaProcessor extends AbstractProcessor {

	@Override
	public Set<String> getSupportedAnnotationTypes() {
		return Set.of(Magic.class.getName());
	}

	@Override
	public SourceVersion getSupportedSourceVersion() {
		return SourceVersion.latestSupported();
	}

	@Override
	public boolean process(final Set<? extends TypeElement> annotations, final RoundEnvironment roundEnv) {
		final Set<? extends Element> elements = roundEnv.getElementsAnnotatedWith(Magic.class);
		for (final Element element : elements) {
			Name elementName = element.getSimpleName();
			if (element.getKind() != ElementKind.INTERFACE) {
				processingEnv.getMessager().printMessage(
					Diagnostic.Kind.ERROR, "Magic annotation can not be used on " + elementName);
			} else {
				processingEnv.getMessager().printMessage(Diagnostic.Kind.NOTE, "Processing " + elementName);
			}
			TypeElement typeElement = (TypeElement) element;
			ClassName className = ClassName.get(typeElement);

			MethodSpec pullOut = MethodSpec.methodBuilder("pullOut")
				.addModifiers(Modifier.PUBLIC)
				.returns(String.class)
				.addStatement("return $S", "Rabbit!")
				.build();

			TypeSpec magicMoja = TypeSpec.classBuilder("MagicMoja")
				.addModifiers(Modifier.PUBLIC)
				.addSuperinterface(className)
				.addMethod(pullOut)
				.build();

			Filer filer = processingEnv.getFiler();
			try {
				JavaFile.builder(className.packageName(), magicMoja)
					.build()
					.writeTo(filer);
			} catch (IOException e) {
				processingEnv.getMessager().printMessage(Diagnostic.Kind.ERROR, "FATAL ERROR: " + e);
			}
		}
		return true;
	}
}
```
Javapoet 을 이용해서 소스 코드를 생성할 수 있다.  

## 애노테이션 프로세서 정리
애노테이션 프로세서 사용 예  
- 롬복  
- AutoService: java.util.ServiceLoader용 파일 생성 유틸리티  
- @Override  
  
[How do annotations like @Override work internally in Java?](https://stackoverflow.com/questions/18189980/how-do-annotations-like-override-work-internally-in-java/18202623)  
@Override는 첫 번째의 예로, Reflection API를 사용하여 슈퍼클래스 중 하나에서 method signature과 일치하는 것을 찾을 수 있는지 확인하고, 찾을 수 없는 경우 `Messager`를 사용하여 컴파일 오류를 발생시킨다.  
  
애노테이션 프로세서 장점  
- 런타임 비용이 제로  
  
애노테이션 프로세서 단점  
- 기존 클래스 코드를 변경할 때는 약간의 hack이 필요하다.  