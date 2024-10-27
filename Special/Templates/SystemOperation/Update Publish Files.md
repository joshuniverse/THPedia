---
created: 2024-08-31T00:38:05-04:00
modified: 2024-08-31T01:02:35-04:00
---

<%*
const dv = app.plugins.plugins["dataview"].api;
const openPublishPanel = app.commands.commands["publish:view-changes"].callback;

// Add as many filenames and queries as you'd like!
const fileAndQuery = new Map([
  [
    "System/Recently edited",
    'TABLE file.name as Name, file.mtime as "Last Modified Date" FROM "Pages" SORT file.mtime DESC',
  ],
  [
    "System/New files",
    'TABLE file.name as Name, file.ctime as Created, file.mtime as Modified FROM "Pages" SORT file.ctime DESC',
  ],
]);

await fileAndQuery.forEach(async (query, filename) => {
  if (!tp.file.find_tfile(filename)) {
    await tp.file.create_new("", filename);
    new Notice(`Created ${filename}.`);
  }
  const tFile = tp.file.find_tfile(filename);
  const queryOutput = await dv.queryMarkdown(query);
  const fileContent = `---\npublish: true\n---\n%% update via "Update Publish Files" template %% \n\n${queryOutput.value}`;
  try {
    await app.vault.modify(tFile, fileContent);
    new Notice(`Updated ${tFile.basename}.`);
  } catch (error) {
    new Notice("⚠️ ERROR updating! Check console. Skipped file: " + filename , 0);
  }
});
openPublishPanel();
%>
