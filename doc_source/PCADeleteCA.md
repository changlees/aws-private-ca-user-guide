# Delete Your Private CA<a name="PCADeleteCA"></a>

You can delete a private CA permanently\. You might want to delete one, for example, to replace it with a new CA that has a new private key\. This is necessary because you cannot update the key by importing an updated CA certificate that is associated with a new key\. If you want to replace a CA, we recommend that you follow this process:

1. Create the replacement CA\.

1. Once the new private CA is in production, disable the old one but do not immediately delete it\.

1. Keep the old CA disabled until all of the certificates issued by it have expired\.

1. Delete the old CA\.

ACM PCA does not check that all of the issued certificates have expired before it processes a delete request\. You can generate an [audit report](PcaAuditReport.md) to determine which certificates have expired\. While the CA is disabled, you can revoke certificates, but you cannot issue new ones\. 

If you must delete a private CA before all the certificates it has issued have expired, we recommend that you also revoke the CA certificate\. The CA certificate will be listed in the CRL of the parent CA, and the private CA will be untrusted by clients\. 

**Important**  
A private CA can be deleted if it is in the `PENDING_CERTIFICATE`, `CREATING`, `DISABLED`, or `FAILED` state\. In order to delete a CA in the `ACTIVE` state, you must first disable it, or the delete request results in an exception\. If you are deleting a private CA in the `PENDING_CERTIFICATE` or `DISABLED` state, you can set the length of its restoration period from 7 to 30 days, with 30 being the default\. During this period, the CA is restorable and its status is set to `DELETED`\. Because a private CA that is deleted in the `CREATING` or `FAILED` state is not given a restoration period, it cannot be restored later\. For more information, see [Restore Your Private CA](PCARestoreCA.md)\.  
You are not charged for a private CA after it has been deleted\. However, if a deleted CA is restored, you are charged for the time between deletion and restoration\. For more information, see [Pricing](PcaPricing.md)\.

You can delete a private CA from the AWS Management Console or AWS CLI:
+ [Deleting a private CA \(console\)](#DeleteCAConsole)
+ [Deleting a private CA \(AWS CLI\)](#DeleteCACli)

**To delete a private CA \(console\)**

1. Sign in to your AWS account and open the ACM PCA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\. 

1. Choose **Private CAs**\.

1. Choose your private CA from the list\.

1. If your CA is in the `ACTIVE` state, you must disable it\. On the **Actions** menu, choose **Disable**\.

1. On the **Actions** menu, choose **Delete**\.

1. If your CA is in the `DISABLED` or `PENDING_CERTIFICATE` state, specify a restoration period from 7 to 30 days, during which the private CA can be restored\. Then choose **Delete**\.
**Note**  
If your private CA is not in one of these states, it can not be restored later\.

1. If you are certain that you want to delete the private CA, choose **Permanently delete** when prompted\. The status of the private CA changes to `DELETED`\. However, you can restore the private CA before the end of the restoration period\. To check the restoration period of a private CA in the `DELETED` state, call the [DescribeCerticateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_DescribeCertificateAuthority.html) or [ListCertificateAuthorities](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_ListCertificateAuthorities.html) operation\.

**To delete a private CA \(AWS CLI\)**  
Use the [delete\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/delete-certificate-authority.html) command to delete a private CA\.

```
aws acm-pca delete-certificate-authority \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/12345678-1234-1234-1234-123456789012 \
--permanent-deletion-time-in-days 16
```