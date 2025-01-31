---
title: Manage Database Users and Roles
summary: Learn how to manage database users and roles in the TiDB Cloud console.
---

# データベースのユーザーとロールの管理 {#manage-database-users-and-roles}

このドキュメントでは、 [TiDB Cloudコンソール](https://tidbcloud.com/)の**[SQL ユーザー]**ページを使用してデータベース ユーザーとロールを管理する方法について説明します。

> **注記：**
>
> -   **SQL ユーザー**ページはベータ版であり、リクエストがあった場合にのみ利用可能です。この機能をリクエストするには、 **「?」**をクリックしてください。 [TiDB Cloudコンソール](https://tidbcloud.com)の右下隅にある**[サポートをリクエスト]**をクリックします。次に、「**説明」**フィールドに「SQL ユーザーページへの申請」と入力し、 **「送信」**をクリックします。
> -   データベース ユーザーとロールは[組織およびプロジェクトのユーザーと役割](/tidb-cloud/manage-user-access.md)から独立しています。データベース ユーザーは TiDB クラスター内のデータベースにアクセスするために使用され、組織およびプロジェクト ユーザーは[TiDB Cloudコンソール](https://tidbcloud.com/)の組織およびプロジェクトにアクセスするために使用されます。
> -   **「SQL ユーザー」**ページに加えて、SQL クライアントを使用してクラスターに接続し、SQL ステートメントを作成することによって、データベース ユーザーとロールを管理することもできます。詳細については、 [TiDB ユーザーアカウント管理](https://docs.pingcap.com/tidb/dev/user-account-management)を参照してください。

## データベースユーザーの役割 {#roles-of-database-users}

TiDB Cloudでは、ロールベースのアクセス制御のために、組み込みロールと複数のカスタムロール (利用可能な場合) の両方を SQL ユーザーに付与できます。

-   組み込みの役割

    TiDB Cloud は、 SQL ユーザーのデータベース アクセスの制御に役立つ次の組み込みロールを提供します。組み込みロールの 1 つを SQL ユーザーに付与できます。

    -   `Database Admin`
    -   `Database Read-Write`
    -   `Database Read-Only`

-   カスタムロール

    組み込みのロールに加えて、クラスターに[`CREATE ROLE`](/sql-statements/sql-statement-create-role.md)ステートメントを使用して作成されたカスタム ロールがある場合は、 TiDB Cloudコンソールで SQL ユーザーを作成または編集するときに、これらのカスタム ロールを SQL ユーザーに付与することもできます。

SQL ユーザーに組み込みロールと複数のカスタム ロールの両方が付与されると、ユーザーの権限は、これらのロールから派生したすべての権限を結合したものになります。

## 前提条件 {#prerequisites}

-   **[SQL ユーザー]**ページを使用してデータベース ユーザーとロールを管理するには、組織の`Organization Owner`ロール、またはプロジェクトの`Project Owner`ロールに属している必要があります。
-   プロジェクトの`Project Data Access Read-Write`または`Project Data Access Read-Only`ロールに属している場合は、そのプロジェクトの**[SQL ユーザー]**ページでデータベース ユーザーのみを表示できます。

## SQLユーザーを作成する {#create-a-sql-user}

SQL ユーザーを作成するには、次の手順を実行します。

1.  [TiDB Cloudコンソール](https://tidbcloud.com/)では、プロジェクトの[**クラスター**](https://tidbcloud.com/console/clusters)ページに移動します。

    > **ヒント：**
    >
    > 複数のプロジェクトがある場合は、<mdsvgicon name="icon-left-projects">左下隅の をクリックして、別のプロジェクトに切り替えます。</mdsvgicon>

2.  クラスター名をクリックし、左側のナビゲーション ペインで**[SQL ユーザー]**をクリックします。

3.  右上隅にある**「SQL ユーザーの作成」**をクリックします。

    SQL ユーザー構成のダイアログが表示されます。

4.  ダイアログで、次のように SQL ユーザーの情報を入力します。

    1.  SQL ユーザーの名前を入力します。
    2.  SQL ユーザーのパスワードを作成するか、 TiDB Cloudにユーザーのパスワードを自動的に生成させます。
    3.  SQL ユーザーにロールを付与します。

        -   **組み込みロール**: [組み込みロール**] ドロップ**ダウン リストで SQL ユーザーの組み込みロールを選択する必要があります。

        -   **カスタム ロール**: クラスターに[`CREATE ROLE`](/sql-statements/sql-statement-create-role.md)ステートメントを使用して作成されたカスタム ロールがある場合は、 **[カスタム ロール]**ドロップダウン リストからロールを選択して、SQL ユーザーにカスタム ロールを付与できます。それ以外の場合、 **[カスタム ロール]**ドロップダウン リストはここには表示されません。

    SQL ユーザーごとに、組み込みロールと複数のカスタム ロール (存在する場合) を付与できます。

5.  **「作成」**をクリックします。

## SQL ユーザーをビュー {#view-sql-users}

クラスターの SQL ユーザーを表示するには、次の手順を実行します。

1.  [TiDB Cloudコンソール](https://tidbcloud.com/)では、プロジェクトの[**クラスター**](https://tidbcloud.com/console/clusters)ページに移動します。

    > **ヒント：**
    >
    > 複数のプロジェクトがある場合は、<mdsvgicon name="icon-left-projects">左下隅の をクリックして、別のプロジェクトに切り替えます。</mdsvgicon>

2.  クラスター名をクリックし、左側のナビゲーション ペインで**[SQL ユーザー]**をクリックします。

## SQLユーザーを編集する {#edit-a-sql-user}

SQL ユーザーのパスワードまたはロールを編集するには、次の手順を実行します。

1.  [TiDB Cloudコンソール](https://tidbcloud.com/)では、プロジェクトの[**クラスター**](https://tidbcloud.com/console/clusters)ページに移動します。

    > **ヒント：**
    >
    > 複数のプロジェクトがある場合は、<mdsvgicon name="icon-left-projects">左下隅の をクリックして、別のプロジェクトに切り替えます。</mdsvgicon>

2.  クラスター名をクリックし、左側のナビゲーション ペインで**[SQL ユーザー]**をクリックします。

3.  編集する SQL ユーザーの行で、 **「アクション」**列の**「...」**をクリックし、 **「編集」**をクリックします。

    SQL ユーザー構成のダイアログが表示されます。

4.  ダイアログで、必要に応じてユーザーのパスワードと役割を編集し、 **「更新」**をクリックします。

    > **注記：**
    >
    > デフォルトの`<prefix>.root`ユーザーのロールは変更をサポートしていません。変更できるのはパスワードのみです。

## SQLユーザーを削除する {#delete-a-sql-user}

SQL ユーザーを削除するには、次の手順を実行します。

1.  [TiDB Cloudコンソール](https://tidbcloud.com/)では、プロジェクトの[**クラスター**](https://tidbcloud.com/console/clusters)ページに移動します。

    > **ヒント：**
    >
    > 複数のプロジェクトがある場合は、<mdsvgicon name="icon-left-projects">左下隅の をクリックして、別のプロジェクトに切り替えます。</mdsvgicon>

2.  クラスター名をクリックし、左側のナビゲーション ペインで**[SQL ユーザー]**をクリックします。

3.  編集する SQL ユーザーの行で、 **「アクション」**列の**「...」**をクリックし、 **「削除」**をクリックします。

    > **注記：**
    >
    > デフォルトの`<prefix>.root`ユーザーは削除をサポートしていません。

4.  削除を確認します。
