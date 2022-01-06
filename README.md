# Cisco DUO 2FA for Nextcloud 21

<!-- wp:heading {"level":4} -->
<h4>Installation Environment</h4>
<!-- /wp:heading -->

<!-- wp:list -->
<ul><li>CENTOS 7</li><li>HTTPD</li><li>PHP 7.4</li><li>Nextcloud 21</li></ul>
<!-- /wp:list -->

<!-- wp:html -->
<br>
<!-- /wp:html -->

<!-- wp:paragraph -->
<p>Open and download files from <a rel="noreferrer noopener" href="https://github.com/ChristophWurst/twofactor_duo" target="_blank">GitHub</a></p>
<!-- /wp:paragraph -->

<!-- wp:image {"width":518,"height":259,"linkDestination":"custom"} -->
<figure class="wp-block-image is-resized"><a href="https://github.com/ChristophWurst/twofactor_duo"><img src="https://aws1.discourse-cdn.com/business6/uploads/duo/optimized/2X/e/e530ce6b619e1c811046dcc05db92056919acd0d_2_690x345.png" alt="" width="518" height="259"/></a></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<br><br>
<p>then extract zip to your Nextcloud path at folder&nbsp;<em><strong>[nextcloud_path]</strong>/apps/twofactor_duo</em></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>=====================================================================</p>
<!-- /wp:paragraph -->

<!-- wp:quote -->
<blockquote class="wp-block-quote"><p>open file&nbsp;<em><strong>[nextcloud_path]</strong>/apps/twofactor_duo/appinfo/info.xml</em></p></blockquote>
<!-- /wp:quote -->

<!-- wp:paragraph -->
<p>Find and <strong>delete</strong>&nbsp;below code:</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>&lt;dependencies&gt;
	&lt;php min-version="5.6" max-version="7.1" /&gt;
	&lt;nextcloud min-version="13" max-version="13" /&gt;
&lt;/dependencies&gt;
</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>and replace this&nbsp;<code>&lt;category&gt;auth&lt;/category&gt;</code>&nbsp;to</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>&lt;<strong>category</strong>&gt;integration&lt;/<strong>category</strong>&gt;
&lt;<strong>category</strong>&gt;security&lt;/<strong>category</strong>&gt;
</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>=====================================================================</p>
<!-- /wp:paragraph -->

<!-- wp:quote -->
<blockquote class="wp-block-quote"><p>next, open&nbsp;<em><strong>[nextcloud_path]</strong>/config/config.php</em></p></blockquote>
<!-- /wp:quote -->

<!-- wp:paragraph -->
<p>add this code before&nbsp;<strong>);</strong></p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>'twofactor_duo' =&gt; &#91;
  'IKEY' =&gt; 'xxxx',
  'SKEY' =&gt; 'xxxxx',
  'HOST' =&gt; 'xxxxx',
  'AKEY' =&gt; 'xxxx',
],
</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>note: for&nbsp;<strong>AKEY</strong>&nbsp;use&nbsp;<strong>IKEY</strong>&nbsp;value</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>=====================================================================</p>
<!-- /wp:paragraph -->

<!-- wp:quote -->
<blockquote class="wp-block-quote"><p>open this files&nbsp;<em><strong>[nextcloud_path]</strong>/lib/public/Authentication/TwoFactorAuth/IProvider.php</em></p></blockquote>
<!-- /wp:quote -->

<!-- wp:paragraph -->
<p>under “<code>interface IProvider</code>” section find all of the public functions and remove the colon and type after the function name<br>example: change “<code>public function getId(): string;</code>” to “<code>public function getId();</code>”. This needs to be done for all six public functions.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>=====================================================================</p>
<!-- /wp:paragraph -->

<!-- wp:quote -->
<blockquote class="wp-block-quote"><p>open&nbsp;<em><strong>[nextcloud_path]</strong>/lib/public/Authentication/TwoFactorAuth/IProvidesCustomCSP.php</em></p></blockquote>
<!-- /wp:quote -->

<!-- wp:paragraph -->
<p>search for “<code>public function getCSP</code>”. Comment out line and put in “<code>public function getCSP();</code>”</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>=====================================================================</p>
<!-- /wp:paragraph -->

<!-- wp:quote -->
<blockquote class="wp-block-quote"><p>open&nbsp;<em><strong>[nextcloud_path]</strong>/lib/public/Authentication/TwoFactorAuth/IProvidesCustomCSP.php</em></p></blockquote>
<!-- /wp:quote -->

<!-- wp:paragraph -->
<p>search for “<code>public function getCSP</code>”. Comment out line and put in “<code>public function getCSP();</code>”</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>=====================================================================</p>
<!-- /wp:paragraph -->

<!-- wp:quote -->
<blockquote class="wp-block-quote"><p>open&nbsp;<em><strong>[nextcloud_path]</strong>/core/Controller/TwoFactorChallengeController.php</em></p></blockquote>
<!-- /wp:quote -->

<!-- wp:paragraph -->
<p>search for “<code>return new StandaloneTemplateRe sponse</code>” and comment out that line, and add this code</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>$response = <strong>new</strong> TemplateResponse(<strong>$this</strong>-&gt;appName, 'twofactorshowchallenge', $data, 'guest');
<strong>if</strong> ($provider <strong>instanceof</strong> IProvidesCustomCSP) {
  $response-&gt;setContentSecurityPolicy($provider-&gt;getCSP());
}
<strong>return</strong> $response;
</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>=====================================================================<br>after this try to enable&nbsp;<strong>twofactor_duo</strong>&nbsp;app, you can do it directly from your&nbsp;<strong>Nextcloud</strong>&nbsp;Apps or use&nbsp;<code>occ</code>&nbsp;from cli</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>cd /nextcloud/path/directory
sudo -u apache php occ app:enable twofactor_duo
</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>=====================================================================<br>use cli<br><code>sudo -u apache php occ integrity:check-core</code><br>you will see&nbsp;<strong>INVALID_HASH</strong>&nbsp;( Failed integrity check, invalid hash)<br>just see under detect file with have invalid hash, had&nbsp;<strong>expected</strong>&nbsp;hash and&nbsp;<strong>current</strong>&nbsp;hash</p>
<!-- /wp:paragraph -->

<!-- wp:quote -->
<blockquote class="wp-block-quote"><p>open&nbsp;<em><strong>[nextcloud_path]</strong>/core/signature.json</em></p></blockquote>
<!-- /wp:quote -->

<!-- wp:paragraph -->
<p>find&nbsp;<strong>expected</strong>&nbsp;hash and then replace with&nbsp;<strong>current</strong>&nbsp;hash, do it all</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>=====================================================================<br>now we must create&nbsp;<em>signature</em>&nbsp;for&nbsp;<strong>twofactor_duo</strong><br>first we must generate&nbsp;<strong>key</strong>&nbsp;and then&nbsp;<strong>crt</strong></p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>sudo openssl genrsa -des3 -out /etc/ssl/twofactor.key 2048
sudo openssl req -x509 -nodes -days 36500 -newkey rsa:2048 -keyout /etc/ssl/twofactor.key -out /etc/ssl/twofactor.crt
sudo -u apache php occ integrity:sign-app --path apps/twofactor_duo --privateKey /etc/ssl/twofactor.key --certificate /etc/ssl/twofactor.crt
</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>=====================================================================<br>try to&nbsp;<strong>logout</strong>&nbsp;and&nbsp;<strong>login</strong>&nbsp;again</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":1} -->
<h1><strong>Good Luck</strong></h1>
<!-- /wp:heading -->

<!-- wp:html -->
<br><br><br>
<!-- /wp:html -->
