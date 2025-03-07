---
title: Use solution checker in Managed Environments (preview)
description: Learn about using solution checker to automatically run security and reliability validations during solution import.
ms.topic: conceptual
ms.date: 05/12/2023
author: sidhartg
ms.author: sidhartg
ms.reviewer: sericks
ms.subservice: admin
ms.custom: 
search.audienceType:
- admin

---

# Use solution checker in Managed Environments (preview)

[!INCLUDE [cc-beta-prerelease-disclaimer](../includes/cc-beta-prerelease-disclaimer.md)]

You can use [solution checker](/power-apps/maker/data-platform/use-powerapps-checker) in Managed Environments to enforce rich static analysis checks on your solutions against a set of best practice rules and identify problematic patterns.

> [!IMPORTANT]
>
> - This is a preview feature.
> - Preview features aren’t meant for production use and may have restricted functionality. These features are available before an official release so that customers can get early access and provide feedback.
> - This feature is being gradually rolled out across regions and might not be available yet in your region.

To enable the solution checker for your Managed Environment:

1. Sign in to the [Power Platform admin center](https://aka.ms/ppac).
1. In the navigation pane, select **Environments**, and then select a managed environment.
1. On the command bar, select **Edit Managed Environments**, and then select the appropriate setting under **Solution checker**.

    :::image type="content" source="media/managed-environment-solution-checker.png" alt-text="Screenshot of the solution checker settings screen.":::

## Solution checker settings

Select one of the following settings:

| Setting | Description |
| --- | --- |
| None |  Turns off the automatic solution validations during solution import. There aren't any experience or behavioral changes to solution authoring, exports, or imports. |
| Warn |  All custom solutions are automatically verified during solution import. When a solution with highly-critical issues is being imported, you are warned about the action but the import itself continues, and if everything else with the import is fine, the solution is imported into the environment. After a successful import, a message stating that the imported solution had validation issues is shown. Additionally, Power Platform environment admins receive a summary email with details of the solution validation. |
| Block | All custom solutions are automatically verified during solution import. When a solution has highly-critical issues, the import process is canceled, and a message stating that the imported solution had validation issues is shown. This happens before the actual import, so there aren't any changes to the environment due to the import failure. Additionally, Power Platform environment admins receive a summary email with details of the solution validation.|

When the solution checker enforcement is turned on, all solutions should be validated explicitly using the solution checker in the source environment before importing into a target environment. Without this step, the verification of solutions fails and in the **Block** mode, solution imports are blocked.

### Considerations
- Solution checker must be run with the solution checker ruleset. The easiest ways to do this are:
  - Run solution checker in the [maker portal](/power-apps/maker/data-platform/use-powerapps-checker) where the solution checker ruleset is used.
  - Use [pac solution check](../developer/cli/reference/solution.md#pac-solution-check) where the solution checker ruleset is used by default.
- Solution checker must be run within a 90 day window of the import.
- When invoking solution checker, do not pass any file exclusions or rule overrides. These may be supported for solution checker enforcement in the future.

## Email messages to the admin

When the validation mode is set to **Warn** or **Block**, Power Platform admins receive summary emails when a solution is imported or blocked. The contents of the email differ depending on the way solution was checked.

Solutions checked from Power Apps [(make.powerapps.com](https://make.powerapps.com)) have the results stored in the source environment. When this solution is imported into an environment, admins of this environment get a link to these results in the summary email.

Solutions checked from the [Power Platform Build Tools](/power-platform/alm/devops-build-tools) have the results returned as a downloadable file of the Power Apps Checker build task. When this solution is imported into an environment, environment admins can see the count of issues in the solution in the summary email. The summary email, in this case, doesn't have a link to the results.  

### Suppress validation emails

By default, emails are sent when a solution fails validation for medium and above severities. When the checkbox is selected, emails aren't sent in warn mode. Emails aren't sent in block mode, as well, except for critical violations which block solution import.

:::image type="content" source="media/managed-environment-solution-checker-checkbox.png" alt-text="Screenshot of the solution checker email checkbox.":::

### Use PowerShell to enable solution checker enforcement

You can use PowerShell to enable solution checker enforcement.

#### Enable solution checker enforcement in block mode

Here's an example PowerShell script that enables solution checker enforcement in block mode. After you run it, the slider shows block mode in the **Solution checker** section of the Managed Environments settings.

```powershell
SetManagedEnvironmentSolutionCheckerEnforcementLevel -EnvironmentId 8d996ece-8558-4c4e-b459-a51b3beafdb4 -Level block
```

#### Enable solution checker enforcement in warn mode

Here's an example PowerShell script that enables solution checker enforcement in warn mode. After you run it, the slider shows warn mode in the **Solution checker** section of the Managed Environments settings.

```powershell
SetManagedEnvironmentSolutionCheckerEnforcementLevel -EnvironmentId 8d996ece-8558-4c4e-b459-a51b3beafdb4 -Level warn
```

#### Turn off solution checker enforcement

Here's an example PowerShell script that turns off solution checker enforcement. After you run it, the slider shows **Off** in the **Solution checker** section of the Managed Environments settings.

```powershell
SetManagedEnvironmentSolutionCheckerEnforcementLevel -EnvironmentId 8d996ece-8558-4c4e-b459-a51b3beafdb4 -Level none
```

### See also

[Managed Environments overview](managed-environment-overview.md) <br />
[Import solutions](/power-apps/maker/data-platform/import-update-export-solutions)  

[!INCLUDE[footer-include](../includes/footer-banner.md)]
