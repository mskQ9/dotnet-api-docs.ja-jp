<span data-ttu-id="e2e5b-101"><xref:System.Text.StringBuilder.Chars%2A> プロパティで文字ベースのインデックス付けを使用すると、次の条件下では非常に遅くなることがあります。</span><span class="sxs-lookup"><span data-stu-id="e2e5b-101">Using character-based indexing with the <xref:System.Text.StringBuilder.Chars%2A> property can be extremely slow under the following conditions:</span></span>

- <span data-ttu-id="e2e5b-102"><xref:System.Text.StringBuilder> インスタンスが大きい (たとえば、数万文字が含まれている)。</span><span class="sxs-lookup"><span data-stu-id="e2e5b-102">The <xref:System.Text.StringBuilder> instance is large (for example, it consists of several tens of thousands of characters).</span></span>
- <span data-ttu-id="e2e5b-103"><xref:System.Text.StringBuilder> が "チャンク化" している。</span><span class="sxs-lookup"><span data-stu-id="e2e5b-103">The <xref:System.Text.StringBuilder> is "chunky."</span></span> <span data-ttu-id="e2e5b-104">つまり、<xref:System.Text.StringBuilder.Append%2A?displayProperty=nameWithType> などのメソッドの反復的な呼び出しにより、オブジェクトの <xref:System.Text.StringBuilder.Capacity%2A?displayProperty=nameWithType> プロパティが自動的に展開され、メモリの新しいチャンクがそれに割り当てられています。</span><span class="sxs-lookup"><span data-stu-id="e2e5b-104">That is, repeated calls to methods such as <xref:System.Text.StringBuilder.Append%2A?displayProperty=nameWithType> have automatically expanded the object's <xref:System.Text.StringBuilder.Capacity%2A?displayProperty=nameWithType> property and allocated new chunks of memory to it.</span></span>

<span data-ttu-id="e2e5b-105">文字にアクセスするたびに、チャンクのリンク リスト全体が走査されて、インデックスを付ける適切なバッファーが検索されるため、パフォーマンスが著しく低下します。</span><span class="sxs-lookup"><span data-stu-id="e2e5b-105">Performance is severely impacted because each character access walks the entire linked list of chunks to find the correct buffer to index into.</span></span>

> [!NOTE]
>  <span data-ttu-id="e2e5b-106">大きな "チャンク化" した <xref:System.Text.StringBuilder> オブジェクトの場合でも、1 つまたは少数の文字へのインデックス ベースのアクセスに <xref:System.Text.StringBuilder.Chars%2A> プロパティを使うと、パフォーマンスへの影響はごくわずかです。通常、これは **0(n)** 操作です。</span><span class="sxs-lookup"><span data-stu-id="e2e5b-106">Even for a large "chunky" <xref:System.Text.StringBuilder> object, using the <xref:System.Text.StringBuilder.Chars%2A> property for index-based access to one or a small number of characters has a negligible performance impact; typically, it is an **0(n)** operation.</span></span> <span data-ttu-id="e2e5b-107"><xref:System.Text.StringBuilder> オブジェクト内の文字を反復処理するときは、パフォーマンスに大きな影響が発生します。これは、**O(n^2)** 操作でます。</span><span class="sxs-lookup"><span data-stu-id="e2e5b-107">The significant performance impact occurs when iterating the characters in the <xref:System.Text.StringBuilder> object, which is an **O(n^2)** operation.</span></span> 

<span data-ttu-id="e2e5b-108"><xref:System.Text.StringBuilder> オブジェクトで文字ベースのインデックス付けを使うときにパフォーマンスの問題が発生する場合は、次のいずれかの回避策を使うことができます。</span><span class="sxs-lookup"><span data-stu-id="e2e5b-108">If you encounter performance issues when using character-based indexing with <xref:System.Text.StringBuilder> objects, you can use any of the following workarounds:</span></span>

- <span data-ttu-id="e2e5b-109"><xref:System.Text.StringBuilder.ToString%2A> メソッドを呼び出して <xref:System.Text.StringBuilder> インスタンスを <xref:System.String> に変換した後、文字列内の文字にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="e2e5b-109">Convert the <xref:System.Text.StringBuilder> instance to a <xref:System.String> by calling the <xref:System.Text.StringBuilder.ToString%2A> method, then access the characters in the string.</span></span>

- <span data-ttu-id="e2e5b-110">既存の <xref:System.Text.StringBuilder> オブジェクトの内容を、事前にサイズを設定した新しい <xref:System.Text.StringBuilder> オブジェクトにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e2e5b-110">Copy the contents of the existing <xref:System.Text.StringBuilder> object to a new pre-sized <xref:System.Text.StringBuilder> object.</span></span> <span data-ttu-id="e2e5b-111">新しい <xref:System.Text.StringBuilder> オブジェクトはチャンク化していないため、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="e2e5b-111">Performance improves because the new <xref:System.Text.StringBuilder> object is not chunky.</span></span> <span data-ttu-id="e2e5b-112">例:</span><span class="sxs-lookup"><span data-stu-id="e2e5b-112">For example:</span></span>

   ```csharp
   // sbOriginal is the existing StringBuilder object
   var sbNew = new StringBuilder(sbOriginal.ToString(), sbOriginal.Length);
   ```
   ```vb
   ' sbOriginal is the existing StringBuilder object
   Dim sbNew = New StringBuilder(sbOriginal.ToString(), sbOriginal.Length)
   ```
- <span data-ttu-id="e2e5b-113"><xref:System.Text.StringBuilder.%23ctor(System.Int32)> コンストラクターを呼び出して、<xref:System.Text.StringBuilder> オブジェクトの初期容量を、予想される最大サイズにほぼ等しい値に設定します。</span><span class="sxs-lookup"><span data-stu-id="e2e5b-113">Set the initial capacity of the <xref:System.Text.StringBuilder> object to a value that is approximately equal to its maximum expected size by calling the <xref:System.Text.StringBuilder.%23ctor(System.Int32)> constructor.</span></span> <span data-ttu-id="e2e5b-114">このようにすると、<xref:System.Text.StringBuilder> が最大容量に達することがほとんどない場合であっても、メモリ ブロック全体が割り当てられることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e2e5b-114">Note that this allocates the entire block of memory even if the <xref:System.Text.StringBuilder> rarely reaches its maximum capacity.</span></span>